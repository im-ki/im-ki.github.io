---
title: ss配置
date: 2018-12-08 14:22:16
tags: [ss]
---
使用了docker, genpac等工具，在DigitalOcean上部署
<!-- more -->
## 服务器端配置
### 直接配置
```bash
sudo apt install shadowsocks
sudo ssserver -p #### -k $$$$ -m %%%%
```
如果要后台运行：
```bash
sudo ssserver -p #### -k $$$$ -m %%%% -d start
```
如果要停止：
```bash
sudo ssserver -d stop
```
如果要检查日志：
```bash
sudo less /var/log/shadowsocks.log
```
如果要开机启动，在/etc/rc.local中加入：
```bash
ssserver -p #### -k $$$$ -m %%%% -d start
```

### 使用Docker
```bash
 $ sudo apt install apt-transport-https ca-certificates curl software-properties-common
 $ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
 $ sudo apt-key fingerprint 0EBFCD88
 $ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
 $ sudo apt update
 $ sudo apt install docker-ce
 $ sudo docker pull oddrationale/docker-shadowsocks
```
或者
```bash
 $ sudo apt install snap
 $ sudo snap install docker
 $ sudo docker pull oddrationale/docker-shadowsocks
```
然后运行
```bash
sudo docker run -d -p ####:#### oddrationale/docker-shadowsocks -s 0.0.0.0 -p #### -k $$$$ -m %%%%
```
其中，####为端口号，$$$$为密码，%%%%为加密方式，常用的有rc4-md5等等

## 客户端设置
```bash
 $ sudo apt install shadowsocks-qt5
 $ sudo apt install python3-pip
 $ sudo pip3 install genpac
 $ genpac --pac-compress --pac-proxy 'SOCKS5 127.0.0.1:1080' --format pac -o ~/.auto.pac
```
然后在系统自动代理的地方填入     
file:///home/系统登录用户名/.auto.pac

## 使用中继服务器
### 可以使用iptables(支持TCP/UDP)
```bash
iptables -t nat -A PREROUTING -p tcp --dport #### -j DNAT --to-destination 1.1.1.1:$$$$
iptables -t nat -A PREROUTING -p udp --dport #### -j DNAT --to-destination 1.1.1.1:$$$$
iptables -t nat -A POSTROUTING -p tcp -d 1.1.1.1 --dport $$$$ -j SNAT --to-source 1.1.1.2
iptables -t nat -A POSTROUTING -p udp -d 1.1.1.1 --dport $$$$ -j SNAT --to-source 1.1.1.2
```
其中，####为中继服务器(1.1.1.2)的端口，$$$$为ss服务器(1.1.1.1)的端口。
如需删除规则，可以运行如下命令：
```bash
iptables -t nat -D POSTROUTING -p udp -d 1.1.1.1 --dport $$$$ -j SNAT --to-source 1.1.1.2
```

### 可以使用haproxy实现中继(仅支持TCP)
安装haproxy
```bash
 $ sudo apt install haproxy
```
配置文件
打开 /etc/haproxy/haproxy.cfg 文件
打开后把里面的内容全部删除，换成下面的内容
```bash
global
 
defaults
    log global
    mode    tcp
    option  dontlognull
        timeout connect 5000
        timeout client  50000
        timeout server  50000
 
frontend ss-in
    bind *:####
    default_backend ss-out
 
backend ss-out
    server server1 $$$.$$$.$$$.$$$:#### maxconn 20480
```
其中$$$.$$$.$$$.$$$是vpn服务器的ip地址，####是服务器的端口
修改过后重新加载该文件：
```bash
sudo service haproxy reload
```
如需关闭，运行：
```bash
sudo systemctl stop haproxy
```

### 使用frp实现中继
首先从[这里](https://github.com/fatedier/frp/releases)下载frp程序

解压
配置中继服务器的配置文件frps.ini
```bash
[common]
bind_port = ####
token = @#$%^&
```
其中，####是frp服务的端口号，token任取。然后运行
```bash
nohup ./frps -c frps.ini >/dev/null 2>&1 &
```

再配置ss服务器的配置文件frpc.ini
```bash
[common]
server_addr = 中继服务器ip
server_port = ####
token = @#$%^&

[web]
type = tcp
local_ip = 127.0.0.1
local_port = ss服务的端口
remote_port = 在中继服务器上用于提供ss服务的端口
use_compression = true

[web2]
type = tcp
local_ip = 127.0.0.1
local_port = ss服务的端口
remote_port = 在中继服务器上用于提供ss服务的端口2
use_compression = true
```
再运行
```bash
nohup ./frpc -c ./frpc.ini >/dev/null 2>&1 &
```
完成
