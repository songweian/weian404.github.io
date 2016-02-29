---
layout: post
title: inux 挂在命令
category: linux
tags: linux
keywords: 
description: 
---
#linux 挂在命令

##挂载ISO镜像
~~~
#mount -o loop ISO文件名 目的目录
#-t 挂载类型
$ mount -o loop -t iso9660 /** /mydisk.iso  /mnt/vcdrom    如：#mount -o loop -t iso9660 /usr/local/tooldisk/mydisk3.iso  /mnt/vcdrom
~~~
