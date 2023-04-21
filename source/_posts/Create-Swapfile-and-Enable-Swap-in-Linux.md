---
title: Create Swapfile and Enable Swap in Linux
date: 2018-12-12 16:21:22
tags: [Linux]
---
In this article, we will learn how to create swap space using a swap file in Linux: this is important in case we don't have a swap partition created on the hard disk.
<!-- more -->
1. In this example, we will create a swap file of size 2GB using the dd command as follows. Note that bs=1024 means read and write up to 1024 bytes at a time and the file size is equal to the number of blocks (filesize = bs * count)
```bash
 $ sudo dd if=/dev/zero of=/root/swapfile bs=16M count=1024
```
 And then set the appropriate permissions on the file; make it readable only by root user as follows.
```bash
  $ sudo chmod 600 /root/swapfile
```
2. Now setup the file for swap space with the mkwap command.
```bash
 $ sudo mkswap /root/swapfile
```
3. Next, enable the swap file and add it to the system as a swap file.
```bash
 $ sudo swapon /root/swapfile
```
4. Afterwards, enable the swap file to be mounted at boot time. Edit the /etc/fstab file and add the following line in it.
```bash
/root/swapfile swap swap defaults 0 0
```
 In the line above, each field means:

 * /mnt/swapfile – device/file name
 * swap – defines device mount point
 * swap – specifies the file-system type
 * defaults – describes the mount options
 * 0 – specifies the option to be used by the dump program
 * 0 – specifies the fsck command option
 
5. To change the swappiness value, add the following line to the file at /etc/sysctl.conf:
```bash
vm.swappiness=60
```
 Start with a value of 10 and increase if it necessary. A typical default value for swappiness is 60. The higher the number (up to 100), the more often the system uses swap.

6. Now verify the swap file was created using the swapon command.
```bash
  $ swapon -s
```
 
