---
title: Linux kernel compilation
date: 2019-10-10 09:55:15
tags: [Linux]
---

### Linux内核编译过程记录
<!--more-->
使用
```bash
 $ make mrproper # 会清除.config
```
或
```bash
 $ make clean # 不会清除.config
```
清除已编译过的文件

将现有的.config文件放到代码根目录
依据现有配置文件配置新内核，新设置用缺省值
```bash
 $ make olddefconfig
```

根据自己的需要修改配置参数
```bash
 $ make menuconfig
```

开始编译安装
```bash
 $ sudo make -j `nproc` && make modules_install INSTALL_MOD_STRIP=1 && make install
```

如需编译头文件
```bash
 $ sudo make headers_install
```
头文件会被编译到代码目录下的usr/include目录下面
也可以指定目录
```bash
 $ sudo make headers_install INSTALL_HDR_PATH=/usr
```
注意事先备份/usr/include

### 删除过时内核
```bash
 $ cd /boot
```
删除过时内核文件，直接rm *.后缀
更新grub
```bash
 $ sudo update-grub
```













