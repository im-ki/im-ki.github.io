---
title: 编译glibc
date: 2020-05-10 09:55:15
tags: [Linux]
---

### glibc编译过程记录
本文参考[这篇文章](https://notes.ponderworthy.com/rebuild-glibc-optimized-for-your-cpu-in-debian-testing-as-a-local-package-version)
<!--more-->
首先使用apt安装编译所需依赖
```bash
sudo apt build-dep glibc
```
新建文件夹,并下载代码
```bash
cd ~/Downloads
mkdir glibc-recompile
cd glibc-recompile
sudo apt-get source glibc
```

修改一些文件以设置优化
```bash
cd glibc-2.24/debian
```
修改rules文件,找到以下两行
```bash
BUILD_CFLAGS = -O2 -g
HOST_CFLAGS = -pipe -O2 -g $(call xx,extra_cflags)
```
改为
```bash
BUILD_CFLAGS = -O2 -march=native -mtune=native
HOST_CFLAGS = -pipe -O2 $(call xx,extra_cflags) -march=native -mtune=native
```
再转到glibc-2.24/debian/sysdeps,找到x32.mk,确保带有CC和CXX的行后面都有native参数,如:
```bash
amd64_CC = $(CC) -m64 -march=native -mtune=native
amd64_CXX = $(CXX) -m64 -march=native -mtune=native
i386_CC = $(CC) -m32 -march=native -mtune=native
i386_CXX = $(CXX) -m32 -march=native -mtune=native
```
还有amd64.mk或i386.mk也进行相似的修改(修改哪个取决于电脑的架构)
再转到glibc-2.24/debian,执行
```bash
dch
```
将第一行改为
```bash
glibc (2.24-11+deb9u3) UNRELEASED; urgency=medium
```
版本号与最好与系统一致,否则可能安装时可能因依赖问题装不上
查看版本号的命令:
```bash
dpkg -l|grep libc6
```
此时可以保存一份修改后的代码.
接着就可以回到代码根目录开始编译了
```bash
debuild -us -uc
```
此命令会在编译完成后进行测试,但有些不影响最终运行的组件在测试时会不通过,因此可以考虑跳过测试
```bash
DEB_BUILD_OPTIONS=nocheck debuild -us -uc
```
debuild的-nc参数好像说可以跳过clean这一步(未验证)
编译完后,会在代码根目录的上一级目录生成一堆deb包,安装即可
```bash
sudo dpkg -i ./\.deb
```
然后重启.
之后系统可能会提示升级新安装的glibc,可以使用以下命令禁止升级:
```bash
sudo apt-mark hold glibc-doc glibc-source libc6 libc6-dbg libc6-dev libc6-dev-i386 libc6-dev-x32 libc6-i386 libc6-pic libc6-x32 nscd
```


