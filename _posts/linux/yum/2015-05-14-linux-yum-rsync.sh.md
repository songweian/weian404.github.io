---
layout: post
title: yum rsync shell script
category: linux
tags: linux
keywords: 
description: 
---
#!/bin/sh

basepath="/usr/local"
#rsync yum log file
logfile="$basepath/yum_rsync.log"
echo "" > $logfile
echo "`date` begin rsync yum .........................." >> $logfile

#
#CentOS官方标准源：
/usr/bin/rsync -avrt rsync://mirrors.ustc.edu.cn/centos/ --exclude-from=$basepath/exclude_centos.list $basepath/centos/  >> $logfile
/bin/echo "`date` yum rsync CentOS is ok!" >> $logfile
#
#rpmforge源：
/usr/bin/rsync -avrt rsync://mirrors.ispros.com.bd/repoforge/ --exclude-from=$basepath/exclude_repoforge.list $basepath/repoforge/  >> $logfile
/bin/echo "`date` yum rsync rpmforge is ok!" >> $logfile
#
#epel源：
/usr/bin/rsync -avrt rsync://mirrors.ustc.edu.cn/epel/ --exclude-from=$basepath/exclude_epel.list $basepath/epel/  >> $logfile
/bin/echo "`date` yum rsync epel is ok!" >> $logfile

/bin/echo "`date` yum rsync all is ok!" >> $logfile
