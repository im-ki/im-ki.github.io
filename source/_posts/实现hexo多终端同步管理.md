---
title: 实现hexo多终端同步管理
date: 2018-12-09 21:48:33
tags: [blog usage]
---
hexo每次生成博客的时候只上传生成的静态网页文件，默认没有多端同步的方法。同时如果本地的博客文件夹丢失了，就无法找回博文了。本文记录了实现多端同步管理hexo博客的方法。
<!--more-->
使用hexo-deployer-git
```bash
 $ npm install hexo-deployer-git --save
```
在项目根目录下的 _config.yml 里面就可以这样配置
```bash
deploy:
- type: git
  repo: git@github.com:<username>/<username>.github.io.git
  branch: master
- type: git
  repo: git@github.com:<username>/<username>.github.io.git
  branch: src
  extend_dirs: /
  ignore_hidden: false
  ignore_pattern:
      public: .
```
这样，在每次写完博客的时候时候使用 hexo g -d 命令就能将所有其他文件发布到 src 分支。
换电脑的时候就能通过 git 重新下载下来整个项目，然后本地切换到远端的 src 分支
```bash
 $ git clone git@github.com:<username>/<username>.github.io.git
 $ mv <username>.github.io.git blog # Optional, if doing so, you should use 'cd blog' instead.
 $ cd <username>.github.io.git
 $ git checkout -b src origin/src
 $ npm install # 根据package.json里的内容安装需要的包
 $ npm ls --depth 0 #查看是否已全部安装完成
```
如果安装出错，可以尝试如下命令：
```bash
 $ npm install --legacy-peer-deps
```
也可能是npm或nodejs的版本过旧，尝试更新，命令如下:
```bash
npm update -g
sudo npm install -g n
sudo n latest
```
完成上述步骤就能重新获得所有的源文件并新建和切换到src分支，使用hexo g -d发布。
然后，在根目录下建立一个deploy文件和一个sync文件，内容如下：
```bash
#!/bin/sh
#rm -rf themes/next/.git
rm -rf .deploy_git
mv .git ../blog_git_temp # 跳过一个由于文件名过长导致发布失败的bug:)
hexo g -d
mv ../blog_git_temp ./.git
```
```bash
#!/bin/sh
git fetch --all
git reset --hard origin/src
```
然后运行:
```bash
 $ chmod +x ./deploy
 $ chmod +x ./sync
```
其中，git fetch从远程下载最新的，而不尝试合并或rebase任何东西，git reset将主分支重置为您刚刚获取的内容。--hard选项更改工作树中的所有文件以匹配origin/src中的文件

这样，每次写完博客时都可以使用./sync来与GitHub同步，用./deploy发布。
此外要注意，要把themes文件夹下的主题目录下的.git文件夹删掉，否则发布博客的时候主题文件夹将不会被上传
