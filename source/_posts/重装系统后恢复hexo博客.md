---
title: 重装系统后恢复hexo博客(Linux)
date: 2018-12-08 11:18:12
tags: [blog usage]
---
重装系统之后，使用备份的hexo博客文件夹来恢复博客
<!-- more -->
### 首先安装node.js和git
```bash
$ sudo apt install nodejs git npm
```
### 使用npm安装hexo
```bash
$ sudo npm install hexo-cli -g
```

### 设置邮箱和用户名，因为重装系统有可能删除了配置文件包括环境变量里面的，没有配置 name 和 email 的话，git 是无法正常工作的。所以首先得重新配置name跟email。
```bash
$ git config --global user.email "your email address"
$ git config --global user.name "your user name"
```
对了，如果上面两条命令fail了的话，记得先用命令git init再输入上面两条命令
### 重新生成私钥并添加至git后测试链接
```bash
$ ssh-keygen -t rsa -C "your email address"
```
.ssh目录下得到了两个文件：id_rsa（私钥）和id_rsa.pub（公钥）
先在你的GitHub右上角你的用户那里找的Settings -> SSH and GPG keys
在SSH keys下新建一个密钥，把id_rsa.pub里面的内容写进去。

### 测试是否成功
```bash
$ ssh -T git@github.com
```
然后又可以像以前一样开心地使用 hexo g -d了
