---
title: 使用Systemd实现开机运行命令
date: 2019-12-14 10:49:29
tags: [Linux]
---

## 编写脚本
编写一个脚本mount-sda6.service，放在/etc/systemd/system/下
```bash
[Unit]
Description=mount sda6 for backup

[Service]
ExecStart=/bin/bash -c "mount -o compress=zstd /dev/sda6 /media/backup"

[Install]
WantedBy=multi-user.target
```
如果ExecStart中运行了某个脚本，记得要赋于脚本运行权限(chmod +x)。

## 查看当前的状态
使用systemctl list-unit-files --type=service命令。
```bash
sudo systemctl list-unit-files --type=service|grep mount-sda6
```
出现如下内容即为正常。
```bash
mount-sda6.service                         disabled
```

## enable服务后使之start
### 加入开机自启动
```bash
sudo systemctl enable mount-sda6
```
### 马上运行该服务
```bash
sudo systemctl start mount-sda6
```

### 查看运行状态
```bash
sudo systemctl status mount-sda6
```
显示如下：
```bash
   Loaded: loaded (/etc/systemd/system/mount-sda6.service; enabled; vendor preset: enabled)
   Active: inactive (dead) since Fri 2019-12-13 22:11:32 CST; 12h ago
 Main PID: 939 (code=exited, status=0/SUCCESS)

12月 13 22:11:32 ki-PC systemd[1]: Started mount sda6 for backup.
```

## 重启，查看服务是否正常自动启动
```bash
sudo reboot
```
