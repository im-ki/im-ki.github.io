---
title: 开机进入grub解决方案
date: 2020-08-25 02:35:11
tags: [Linux]
---

输入
```bash
ls
```
获取可用的磁盘, 如(hd0,gpt1)之类的. 然后输入
```bash
ls (hd0,gpt1)/
```
查看磁盘内的文件路径, 找到grub所在的路径, 如(hd0,gpt1)/boot/grub或(hd0,gpt1)/grub)或(hd0,gpt1)/EFI/boot/grub或(hd0,gpt1)/EFI/grub, 然后输入
```bash
set
```
查看root和prefix的内容, 确定要修改的变量. 确定了之后使用如下命令:
```bash
set prefix=(hd0,gpt1)/grub
set root=(hd0,gpt1)
insmod linux
insmod normal
normal
```
此时应该可以正常启动了
在进入系统后打开terminal, 使用命令:
```bash
sudo update-grub
```
修复即可
