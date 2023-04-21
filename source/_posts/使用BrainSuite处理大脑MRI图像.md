---
title: 使用BrainSuite处理大脑MRI图像
date: 2021-05-26 18:26:44
tags: 
---

## 需要的软件

1. BrainSuite
2. Brainstorm
<!--more-->

## 导入

打开terminal, 输入BrainSuite的路径打开软件

```shell
/opt/BrainSuite19b/bin/BrainSuite19b
```

软件打开后，选择Actions中的"Open a 3D image file"，然后选择要处理的MRI文件。

## 处理

打开图像后，选择上方菜单栏的"Cortex" -> "Cortical Surface Extraction Sequence"

在弹出的对话框左侧勾选"Register and label brain"

右侧的"Labeled Atlas"下拉菜单选择"BCI-DNI_brain_atlas"，然后点击最上方的"Run All"即可开始分割。

在CPU为洋垃圾E3-1230V2的电脑上，执行BCI-DNI_brain_atlas耗时约53分钟，而执行BrainSuiteAtlas1耗时约23分钟。

结束之后，软件会在图像所在的目录生成一堆文件，其中的*.svreg.label.nii.gz是带有label的文件。

## 分离出hippocampus的mesh

打开terminal，cd到Brainstorm的安装目录下的二进制文件目录，本文的是"/usr/local/brainstorm3/bin/R2020a"

在该目录下执行

```shell
./brainstorm3.command
```

打开Brainstorm

点击菜单上的"File" -> "New protocol"，取一个protocol的名字，下面的两个选项都选No，点"Create"。

右键点击protocol名称下的"Default anatomy"，选"import anatomy folder (auto)"，选择刚才生成*.svreg.label.nii.gz的目录，Filter选"BrainSuite + Volume atlases"，点"OK"即可。

软件读取完数据后，最下方会有"subcortical"，双击它或者右键选"Display"打开大脑的彩色图像。

在主界面的右侧的scout中选择hippocampus对应的编号，具体的对应关系可以在图像目录下的"brainsuite_labeldescription.xml"中找到。注意有wm(白质)和gm(灰质)两种，还有没有标明灰白质的一项。

选择hippocampus对应的编号，点击右边的"ALL"下的"SEL"可以看到所选的区域的图像。

选好编号以后，点击上方"Scout"下的"Scout"(夹在Atlas和Sources中间的那个)，"Edit surface" -> "Keep only selected scouts"。主界面左边会多出一个"subcortical | keep"，右键点击它，选"File" -> "Export to file"，选择文件存放的位置和格式即可。

注：MeshLab支持其中的off格式。
