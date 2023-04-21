---
title: Linux中查看已保存的WiFi密码
date: 2020-01-07 13:44:12
tags: [Linux]
---
```bash
cd /etc/NetworkManager/system-connections
```
使用 cat 命令查看 Linux 中已保存的 WiFi 配置文件，在 WiFi-Security 段的 psk 位置可以查看到无线密码。
