---
title: Turn off system bell
date: 2021-04-19 20:34:20
tags: [Linux]
---

# Step 1. Blacklist pcspkr module
Create /etc/modprobe.d/pcspkr-blacklist.conf:
```bash
blacklist pcspkr
```

# Step 2. Generate a list of module dependencies:
```bash
 $ sudo depmod -a
```

# Step 3. Update an initramfs image:
```bash
 $ sudo update-initramfs -u
```

# Step 4. Reboot.
