---
title: 使用LVM合并三个硬盘并挂载到home目录
date: 2018-12-07 21:27:25
tags: [Linux]
---
目标：服务器里有三个硬盘，想要把三个硬盘合成一个盘，作为home目录，用于存储数据。
<!--more-->

## 1、建立LVM卷
### 使用fdisk -l命令查看机器的物理磁盘
#### 可以看到有三个硬盘分别为/dev/sdb1, /dev/sdc1, /dev/sdd1

### 使用pvcreate将三个物理磁盘加入物理卷
``` bash
$ pvcreate  /dev/sdb1
 Physical volume "/dev/sdb1" successfully created
$ pvcreate  /dev/sdc1
 Physical volume "/dev/sdc1" successfully created
$ pvcreate  /dev/sdd1
 Physical volume "/dev/sdd1" successfully created
```

### 创建逻辑卷组diskgroup，并将/dev/sdc1和/dev/sdd1加入其中
```bash
$ vgcreate diskgroup /dev/sdb1
  Volume group "diskgroup" successfully created
$ vgextend diskgroup /dev/sdc1
  Volume group "diskgroup" successfully extended
$ vgextend diskgroup /dev/sdd1
  Volume group "diskgroup" successfully extended
```

### 使用pvdisplay查看/dev/sdb /dev/sdc的Free PE
```bash
$ pvdisplay
  --- Physical volume ---
  PV Name               /dev/sdb1
  VG Name               diskgroup
  PV Size               931.51 GiB / not usable 4.00 MiB
  Allocatable           yes (but full)
  PE Size               4.00 MiB
  Total PE              238466
  Free PE               0
  Allocated PE          238466
  PV UUID               eyz1EB-nqCw-LHi2-VvAh-KWQw-QhEP-JgpHSL
   
  --- Physical volume ---
  PV Name               /dev/sdc1
  VG Name               diskgroup
  PV Size               298.09 GiB / not usable 1.84 MiB
  Allocatable           yes (but full)
  PE Size               4.00 MiB
  Total PE              76310
  Free PE               0
  Allocated PE          76310
  PV UUID               gAT2xS-dv3c-y8uj-9MAF-WV0d-RxCc-cVFuUC
   
  --- Physical volume ---
  PV Name               /dev/sdd1
  VG Name               diskgroup
  PV Size               298.09 GiB / not usable 4.00 MiB
  Allocatable           yes (but full)
  PE Size               4.00 MiB
  Total PE              76310
  Free PE               0
  Allocated PE          76310
  PV UUID               l045Td-ABgH-9DSv-ymg4-82gj-wXs9-MNb2QG
```

### 使用vgdisplay查看两块物理磁盘合并后的Free PE SIZE大小通用为391086
```bash
$ vgdisplay
  --- Volume group ---
  VG Name               diskgroup
  System ID             
  Format                lvm2
  Metadata Areas        3
  Metadata Sequence No  4
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                1
  Open LV               1
  Max PV                0
  Cur PV                3
  Act PV                3
  VG Size               1.49 TiB
  PE Size               4.00 MiB
  Total PE              391086
  Alloc PE / Size       391086 / 1.49 TiB
  Free  PE / Size       0 / 0   
  VG UUID               7HUui6-rRDa-24io-tiS0-fIB6-KCaP-EaeJ9z
```

### 然后创建卷：lvcreate -l +391086 -n vg01 diskgroup ，其中391086为Free PE SIZE的大小，diskgroup 为创建的卷组名
```bash
$ lvcreate -l +391086 -n vg01 diskgroup
 Logical volume "vg01" created
```
 
### 使用lvdisplay命令获取到LV Path /dev/diskgroup/vg01，并使用mkfs命令对其进行格式化
```bash
$ lvdisplay
  --- Logical volume ---
  LV Path                /dev/diskgroup/vg01
  LV Name                vg01
  VG Name                diskgroup
  LV UUID                671ZPP-uXtN-lmoz-N2oU-WdTT-xONu-W3aknc
  LV Write Access        read/write
  LV Creation host, time weixin-System-Product-Name, 2018-12-07 20:28:55 +0800
  LV Status              available
  # open                 1
  LV Size                1.49 TiB
  Current LE             391086
  Segments               3
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           253:0
$ mkfs -t ext4 /dev/diskgroup/vg01
```
## 2、挂载到home目录
### 新建一个home1目录
```bash
$ cd /
$ mkdir /home1
```

### 挂载
```bash
$ mount /dev/diskgroup/vg01 /home1
```

### 移动home目录下的所有东西到home1
```bash
$ mv /home/* /home1/
```

### 删除home目录，卸载vg01，将home1重命名为home，重新挂载
```bash
$ rm -r home
$ umount home1
$ mv home1 home
$ mount /dev/diskgroup/vg01 /home
```

## 3、设置开机挂载
```bash
$ vi /etc/fstab
```
在文件末尾加上以下内容
```bash
/dev/diskgroup/vg01 /home ext4 defaults 0 1
```
