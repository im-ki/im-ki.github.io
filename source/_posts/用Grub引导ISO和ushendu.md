---
title: 用Grub引导ISO和ushendu.md
date: 2020-05-12 21:32:03
tags: [Linux]
---

ISO文件放在某个分区的根目录,在本文中是sda7,ushendu在sda8

修改文件/etc/grub.d/40_custom(不要直接改文件/boot/grub/grub.cfg),加上以下几行
```bash
menuentry "Debian live" {
    set isofile="/debian-live-10.2.0-amd64-xfce.iso"
    loopback loop (hd0,7)$isofile
    linux (loop)/live/vmlinuz-4.19.0-6-amd64 boot=live config union=overlay username=user components noswap noeject vga=788 ip= net.ifnames=0 toram=filesystem.squashfs findiso=$isofile
    initrd (loop)/live/initrd.img-4.19.0-6-amd64
}
menuentry "UShenDu" --class windows --class os {
    insmod part_gpt
    insmod fat
    set root='hd0,gpt8'
    chainloader /EFI/boot/bootx64.efi
}
```
再
```bash
sudo update-grub
```
