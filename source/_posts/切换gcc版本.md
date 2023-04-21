---
title: 切换gcc版本
date: 2020-09-26 15:01:58
tags: [Linux]
---

### 安装所需版本的gcc和g++
```bash
sudo apt install gcc-5 g++-5
```

### 执行如下指令
```bash
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-5 7
sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-5 7
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-7 8
sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-7 8
```
此处假设原版本为7

### 执行如下指命更换版本

```bash
sudo update-alternatives --config gcc
sudo update-alternatives --config g++
```
