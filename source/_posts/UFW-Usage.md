---
title: UFW Usage
date: 2019-12-14 17:12:28
tags: [Linux]
---

### Enable and disable
```bash
sudo ufw enable
sudo ufw disable
```

### Check the status of UFW
```bash
sudo ufw status verbose
sudo ufw status
```

### Add rules
```bash
sudo ufw allow 53
sudo ufw allow 53/tcp
sudo ufw deny 53
sudo ufw deny 53/tcp
sudo ufw allow 7100:7200/tcp
sudo ufw allow ssh
sudo ufw deny ssh
sudo ufw allow from 207.46.232.182
sudo ufw allow from 192.168.1.0/24
sudo ufw allow from 192.168.0.4 to any port 22
sudo ufw allow from 192.168.0.4 to any port 22 proto tcp
sudo ufw allow in on eth0 to any port 80
sudo ufw allow in on eth0 from 192.168.0.0/16
```
### Delete rules
Add 'delete' after 'ufw' in the above command.
```bash
sudo ufw delete deny 80/tcp
sudo ufw delete allow ssh
```

### Reset
```bash
sudo ufw reset
```
