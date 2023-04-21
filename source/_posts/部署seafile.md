---
title: 部署seafile
date: 2020-06-03 23:58:48
tags: [linux]
---

首先按[这里](https://docs.seafile.com/published/seafile-manual-cn/docker/%E7%94%A8Docker%E9%83%A8%E7%BD%B2Seafile.md)使用docker部署seafile
注意将映射到宿主机的端口换成80以外的端口(如81),避免与后面的caddy冲突
启动后在浏览器登录,在"头像"->"系统管理"->"设置"中,将"FILE_SERVER_ROOT"中的域名换成ip地址,就可以上传下载文件了.
还有两步验证什么的.

docker-compose的一些常用命令:
```bash
docker-compose up -d
docker-compose down   #销毁
docker-compose start
docker-compose stop
docker-compose ps     #查看docker镜像
```

然后安装caddy,生成证书
```bash
echo "deb [trusted=yes] https://apt.fury.io/caddy/ /"     | sudo tee -a /etc/apt/sources.list.d/caddy-fury.list
sudo apt install caddy
openssl req -x509 -newkey rsa:2048 -sha256 -nodes -keyout key.pem -out cert.pem -days 3650 -subj "/CN=****"
```
\*\*\*\*为ip地址
Caddyfile:
```bash
:443 {
tls cert.pem key.pem
reverse_proxy 127.0.0.1:81
}

:444 {
tls cert.pem key.pem
reverse_proxy 127.0.0.1:82
} #假如有其他端口也要使用https
```
\*\*\*\*为ip地址

然后,
```bash
caddy run   #测试是否正常
caddy start #后台运行
```
根据[这里](https://seafile.gitbook.io/seafile-server-manual/security-and-auditing/config-fail2ban)配置fail2ban,由于https是用caddy在docker外实现的,因此iptables可以正常起作用.

根据[这里](https://manual-cn-origin.seafile.com/deploy_pro/office-wen-jian-yu-lan-he-bian-ji/only_office)设置seafile的nginx和seahub_settings.py,其中nginx的ip地址改为网卡docker0的ip,端口为onlyoffice在宿主机的端口,在本文中为780.

然后在seafile的docker-compose.yml中加入:
```bash
  onlyofficeds:
    image: onlyoffice/documentserver:latest
    container_name: oods
    ports:
      - "780:80"
    volumes:
      - /root/seafile/oods/default.json:/etc/onlyoffice/documentserver/default.json
    networks:
      - oods-net
```
并在文件末尾的networks:处改为:
```bash
networks:
  seafile-net:
  oods-net:
```

其中default.json文件那行可以先不写,运行容器的时候从容器中复制一份出来之后找到rejectUnauthorized并改为false.(使用了自签名证书时需要此步)
也可以在容器内执行:
```bash
/var/www/onlyoffice/documentserver/npm/json -f /etc/onlyoffice/documentserver/default.json -I -e 'this.services.CoAuthoring.requestDefaults.rejectUnauthorized=false'
```

或者,使用snap部署onlyoffice:
首先根据[这里](https://snapcraft.io/docs/installing-snap-on-centos)安装snap.
然后依次执行:
```bash
snap install onlyoffice-ds
snap set onlyoffice-ds onlyoffice.ds-port=780
vi /var/snap/onlyoffice-ds/current/etc/onlyoffice/documentserver/default.json
```
将rejectUnauthorized改为false即可.

最后,在caddy配置文件中加入
```bash
    basicauth /onlyofficeds/* {
        用户名 hash码
    }
```
