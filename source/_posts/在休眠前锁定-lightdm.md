---
title: 在休眠前锁定(lightdm)
date: 2021-04-14 23:23:55
tags: [Linux]
---

新建/lib/systemd/system/dmlock.service, 内容如下：
```bash
[Unit]
Description=DM Lock before sleep
Before=sleep.target

[Service]
Environment="XDG_SEAT_PATH=/org/freedesktop/DisplayManager/Seat0"
ExecStart=/usr/bin/dm-tool lock

[Install]
WantedBy=sleep.target
```
其中XDG_SEAT_PATH的值可以由命令 dm-tool list-seats 确定。

然后
```bash
sudo systemctl enable dmlock.service
```
也可以测试效果
```bash
sudo systemctl start dmlock.service
```
