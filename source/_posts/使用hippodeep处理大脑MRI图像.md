---
title: 使用hippodeep处理大脑MRI图像
date: 2021-05-26 20:05:30
tags:
---

## 需要的软件

1. hippodeep-pytorch
2. ITK-SNAP
<!--more-->

## 处理

按照github上的指示安装好PyTorch, SciPy和nibabel后，在仓库目录下执行

```bash
./deepseg1.sh ***.nii.gz
```

其中***.nii.gz为所要处理的文件路径。

程序会生成\*\*\*.mask_L.nii.gz(带有分割的文件，L对应左脑，R对应右脑)

## 分离出hippocampus的mesh

### 使用ITK-SNAP

启动ITK-SNAP

点击菜单栏"File" -> "Open Main Image"打开对话框

点Browse选择要处理的MRI文件，点"Next" -> "Finish"

点击菜单栏"Segmentation" -> "Open Segmentation"打开对话框

点Browse选择刚才hippodeep生成的mask文件，点"Next" -> "Finish"

点击mask区域，可以看到左侧"Label under cursor"为255，而点击其它区域时则为0

点击菜单栏"Segmentation" -> "Export as Surface Mesh"打开对话框，选择"Export a mesh for a single label"，下面选择"Label 255"，点"Next"填妥名称即可。

注：MeshLab支持其中的STL格式
