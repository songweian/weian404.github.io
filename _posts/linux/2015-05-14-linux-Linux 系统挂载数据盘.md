---
layout: post
title: Linux 系统挂载数据盘
category: linux
tags: linux
keywords: 
description: 
---
二、Linux 系统挂载数据盘：

适用系统：Linux（Redhat , CentOS，Debian，Ubuntu）
*  Linux的云服务器数据盘未做分区和格式化，可以根据以下步骤进行分区以及格式化操作。

下面的操作将会把数据盘划分为一个分区来使用。


1、查看数据盘。在没有分区和格式化数据盘之前，使用 “df –h”命令，是无法看到数据盘的，可以使用“fdisk -l”命令查看。如下图：

[root@iZ25hfz3698Z ~]# df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/xvda1       20G  1.4G   18G   8% /
tmpfs            32G     0   32G   0% /dev/shm
[root@iZ25hfz3698Z ~]# fdisk -l

Disk /dev/xvda: 21.5 GB, 21474836480 bytes
255 heads, 63 sectors/track, 2610 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x00078f9c

    Device Boot      Start         End      Blocks   Id  System
/dev/xvda1   *           1        2611    20970496   83  Linux

Disk /dev/xvdb: 536.9 GB, 536870912000 bytes
255 heads, 63 sectors/track, 65270 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x00000000
友情提示：若您执行fdisk -l命令，发现没有 /dev/xvdb 标明您的云服务无数据盘，那么您无需进行挂载，此时该教程对您不适用
2、 对数据盘进行分区。执行“fdisk -S 56 /dev/xvdb”命令，对数据盘进行分区；根据提示，依次输入“n”，“p”“1”，两次回车，“wq”，分区就开始了，很快就会完成。

[root@iZ25hfz3698Z ~]# fdisk -S 56 /dev/xvdb
Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
Building a new DOS disklabel with disk identifier 0x9dab9a2d.
Changes will remain in memory only, until you decide to write them.
After that, of course, the previous content won't be recoverable.

Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
         switch off the mode (command 'c') and change display units to
         sectors (command 'u').

Command (m for help): n
Command action
   e   extended
   p   primary partition (1-4)
p
Partition number (1-4): 1
First cylinder (1-73429, default 1):
Using default value 1
Last cylinder, +cylinders or +size{K,M,G} (1-73429, default 73429):
Using default value 73429

Command (m for help): wq
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
[root@iZ25hfz3698Z ~]# 
 

3、 查看新的分区。

使用“fdisk -l”命令可以看到，新的分区xvdb1已经建立完成了。

[root@iZ25hfz3698Z ~]# fdisk -l

Disk /dev/xvda: 21.5 GB, 21474836480 bytes
255 heads, 63 sectors/track, 2610 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x00078f9c

    Device Boot      Start         End      Blocks   Id  System
/dev/xvda1   *           1        2611    20970496   83  Linux

Disk /dev/xvdb: 536.9 GB, 536870912000 bytes
255 heads, 56 sectors/track, 73429 cylinders
Units = cylinders of 14280 * 512 = 7311360 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x9dab9a2d

    Device Boot      Start         End      Blocks   Id  System
/dev/xvdb1               1       73429   524283032   83  Linux

4、格式化新分区。
使用“mkfs.ext4 /dev/xvdb1”命令对新分区进行格式化，格式化的时间根据硬盘大小有所不同。(也可自主决定选用 ext3 格式)
[root@iZ25hfz3698Z ~]# mkfs.ext4 /dev/xvdb1
mke2fs 1.41.12 (17-May-2010)
文件系统标签=
操作系统:Linux
块大小=4096 (log=2)
分块大小=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
32768000 inodes, 131070758 blocks
6553537 blocks (5.00%) reserved for the super user
第一个数据块=0
Maximum filesystem blocks=4294967296
4000 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208,
4096000, 7962624, 11239424, 20480000, 23887872, 71663616, 78675968,
102400000

正在写入inode表: 完成                           
Creating journal (32768 blocks): 完成
Writing superblocks and filesystem accounting information: 完成

This filesystem will be automatically checked every 25 mounts or
180 days, whichever comes first.  Use tune2fs -c or -i to override.
[root@iZ25hfz3698Z ~]# 
 
5、添加分区信息。
[root@iZ25hfz3698Z ~]#
[root@iZ25hfz3698Z ~]# mkdir /mnt/kcb
使用“echo '/dev/xvdb1  /mnt/dir ext4    defaults    0  0' >> /etc/fstab”（不含引号）命令写入新分区信息。
然后使用“cat /etc/fstab”命令查看，出现以下信息就表示写入成功。
[root@iZ25hfz3698Z ~]# echo '/dev/xvdb1  /mnt/kcb ext4    defaults    0  0' >> /etc/fstab
[root@iZ25hfz3698Z ~]# 
[root@iZ25hfz3698Z ~]# cat /etc/fstab

#
# /etc/fstab
# Created by anaconda on Thu Aug 14 21:16:42 2014
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
UUID=94e4e384-0ace-437f-bc96-057dd64f42ee / ext4 defaults,barrier=0 1 1
tmpfs                   /dev/shm                tmpfs   defaults        0 0
devpts                  /dev/pts                devpts  gid=5,mode=620  0 0
sysfs                   /sys                    sysfs   defaults        0 0
proc                    /proc                   proc    defaults        0 0
none                  /proc/xen               xenfs   defaults        0 0
/dev/xvdb1  /mnt/kcb ext4    defaults    0  0
[root@iZ25hfz3698Z ~]#

注：ubuntu12.04不支持barrier，所以正确写法是：echo '/dev/xvdb1  /mnt ext3    barrier=0  0  0' >> /etc/fstab
*  如果需要把数据盘单独挂载到某个文件夹，比如单独用来存放网页，可以修改以上命令中的/mnt部分

6、挂载新分区。
使用“mount -a”命令挂载新分区，然后用“df -h”命令查看，出现以下信息就说明挂载成功，可以开始使用新的分区了。

[root@iZ25hfz3698Z ~]# mount -a
[root@iZ25hfz3698Z ~]# 
[root@iZ25hfz3698Z ~]# df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/xvda1       20G  1.4G   18G   8% /
tmpfs            32G     0   32G   0% /dev/shm
/dev/xvdb1      493G  198M  467G   1% /mnt/kcb
[root@iZ25hfz3698Z ~]# 


>>Linux FTP服务配置

>>linux一键安装web环境全攻略

>>硬盘分区和格式化for Windows 2008

