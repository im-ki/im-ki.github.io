---
title: Disable core dump
date: 2021-04-19 22:05:36
tags: [Linux]
---

Install systemd-coredump
```bash
sudo apt install systemd-coredump
```

Modify /etc/systemd/coredump.conf
```
[Coredump]
Storage=none
ProcessSizeMax=0
```

Save the file and run the following command:
```bash
 $ systemctl daemon-reload
```

