---
layout: post
title: yum rsync
category: linux
tags: linux
keywords: 
description: 
---
#yum镜像搭建
##系统约定
apache站点根目录`/usr/local/html`
~~~
basepath="/usr/local/html"
~~~
服务器执行脚本文件存放目录:`$basepath/crontab`
##1.安装apache服务器（略），详见：[blog](http://www.baidu.com)

##2.创建镜像文件存放目录
~~~
mkdir -p $basepath/centos #存放CentOS官方标准源

mkdir -p $basepath/repoforge #存放第三方rpmforge源

mkdir -p $basepath/epel #存放第三方epel源
~~~

##3.确定以上三个yum源上游源的同步镜像地址
~~~
1、CentOS官方标准源：
    rsync://mirrors.ustc.edu.cn/centos/
2、rpmforge源：
    rsync://mirrors.ispros.com.bd/repoforge/
3、epel源：
    rsync://mirrors.ustc.edu.cn/epel/
~~~
>上游yum源必须要支持rsync协议，否则不能使用rsync进行同步。
>参考：<br/>
>CentOS官方标准源：<br/>
>rsync://mirrors.kernel.org/centos
>rpmforge源：<br/>
>http://apt.sw.be/<br/>
>rsync://ftp-stud.fht-esslingen.de/dag<br/>
>epel源：<br/>
>http://mirrors.fedoraproject.org/publiclist/EPEL/<br/>
>rsync://mirrors.kernel.org/fedora-epel<br/>

##4、创建yum源同步脚本
~~~
mkdir -p $basepath/crontab #创建目录

vi /home/crontab/yum_rsync.sh #添加以下代码
~~~
~~~
#!/bin/sh

/usr/bin/rsync -avrt rsync://mirrors.ustc.edu.cn/centos/ --exclude-from=/usr/local/nginx/html/exclude_centos.list /usr/local/nginx/html/centos/

/usr/bin/rsync -avrt rsync://mirrors.ispros.com.bd/repoforge/ --exclude-from=/usr/local/nginx/html/exclude_repoforge.list /usr/local/nginx/html/repoforge/

/usr/bin/rsync -avrt rsync://mirrors.ustc.edu.cn/epel/ --exclude-from=/usr/local/nginx/html/exclude_epel.list /usr/local/nginx/html/epel/
~~~
~~~
:wq! #保存退出
chmod u+x $basepath/crontab/yum_rsync.sh #添加脚本执行权限
~~~

>运行此脚本前，先要创建好同步目录及不需要同步的目录列表文件
~~~
touch $basepath/exclude_centos.list
touch $basepath/exclude_repoforge.list
touch $basepath/exclude_epel.list
~~~
把不需要同步的目录写到上面对应的文件中，每行一个目录
例如：
~~~
vi exclude_epel.list
~~~
exclude_epel.list内容
~~~
4/
4AS/
4ES/
4WS/
~~~
~~~
:wq! #保存退出
~~~
##5、添加脚本定时执行任务
~~~
vi /etc/crontab  #在最后一行添加以下代码
~~~
内容
~~~
0 1 * * * root /home/crontab/yum_rsync.sh #设置每天凌晨1点整开始执行脚本
~~~

service crond restart #重启

测试篇：

一、安装rsync同步软件

yum install rsync xinetd #安装

vi /etc/xinetd.d/rsync #编辑配置文件，设置开机启动rsync

disable = no #修改为

/etc/init.d/xinetd start #启动（CentOS中是以xinetd 来管理Rsync服务的）

:wq! #保存退出

二、执行同步脚本

sh /home/crontab/yum_rsync.sh

注意：等待脚本执行完毕，首次同步，耗费的时间比较长！

三、根据不同版本创建三个yum源的repo配置文件

cd /etc/yum.repos.d/ #进入目录

mv /etc/yum.repos.d/CentOS-Base.repo CentOS-Base.repo-bak

1、CentOS官方标准源：

CentOS 5.x系列：

vi /etc/yum.repos.d/CentOS-Base.repo #添加以下代码

# CentOS-Base.repo

#

# The mirror system uses the connecting IP address of the client and the

# update status of each mirror to pick mirrors that are updated to and

# geographically close to the client. You should use this for CentOS updates

# unless you are manually picking other mirrors.

#

# If the mirrorlist= does not work for you, as a fall back you can try the

# remarked out baseurl= line instead.

#

#

[base]

name=CentOS-$releasever - Base - huanqiu.com

baseurl=http://192.168.21.188/centos/$releasever/os/$basearch/

#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os

gpgcheck=1

gpgkey=http://192.168.21.188/centos/RPM-GPG-KEY-CentOS-5

#released updates

[updates]

name=CentOS-$releasever - Updates - huanqiu.com

baseurl=http://192.168.21.188/centos/$releasever/updates/$basearch/

#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates

gpgcheck=1

gpgkey=http://192.168.21.188/centos/RPM-GPG-KEY-CentOS-5

#packages used/produced in the build but not released

[addons]

name=CentOS-$releasever - Addons - huanqiu.com

baseurl=http://192.168.21.188/centos/$releasever/addons/$basearch/

#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=addons

gpgcheck=1

gpgkey=http://192.168.21.188/centos/RPM-GPG-KEY-CentOS-5

#additional packages that may be useful

[extras]

name=CentOS-$releasever - Extras - huanqiu.com

baseurl=http://192.168.21.188/centos/$releasever/extras/$basearch/

#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras

gpgcheck=1

gpgkey=http://192.168.21.188/centos/RPM-GPG-KEY-CentOS-5

#additional packages that extend functionality of existing packages

[centosplus]

name=CentOS-$releasever - Plus - huanqiu.com

baseurl=http://192.168.21.188/centos/$releasever/centosplus/$basearch/

#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus

gpgcheck=1

enabled=0

gpgkey=http://192.168.21.188/centos/RPM-GPG-KEY-CentOS-5

#contrib - packages by Centos Users

[contrib]

name=CentOS-$releasever - Contrib - huanqiu.com

baseurl=http://192.168.21.188/centos/$releasever/contrib/$basearch/

#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=contrib

gpgcheck=1

enabled=0

gpgkey=http://192.168.21.188/centos/RPM-GPG-KEY-CentOS-5

:wq! #保存退出

#########################

CentOS 6.x系列：

vi /etc/yum.repos.d/CentOS-Base.repo #添加以下代码

# CentOS-Base.repo

#

# The mirror system uses the connecting IP address of the client and the

# update status of each mirror to pick mirrors that are updated to and

# geographically close to the client. You should use this for CentOS updates

# unless you are manually picking other mirrors.

#

# If the mirrorlist= does not work for you, as a fall back you can try the

# remarked out baseurl= line instead.

#

#

[base]

name=CentOS-$releasever - Base - huanqiu.com

baseurl=http://192.168.21.188/centos/$releasever/os/$basearch/

#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os

gpgcheck=1

gpgkey=http://192.168.21.188/centos/RPM-GPG-KEY-CentOS-6

#released updates

[updates]

name=CentOS-$releasever - Updates - huanqiu.com

baseurl=http://192.168.21.188/centos/$releasever/updates/$basearch/

#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates

gpgcheck=1

gpgkey=http://192.168.21.188/centos/RPM-GPG-KEY-CentOS-6

#additional packages that may be useful

[extras]

name=CentOS-$releasever - Extras - huanqiu.com

baseurl=http://192.168.21.188/centos/$releasever/extras/$basearch/

#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras

gpgcheck=1

gpgkey=http://192.168.21.188/centos/RPM-GPG-KEY-CentOS-6

#additional packages that extend functionality of existing packages

[centosplus]

name=CentOS-$releasever - Plus - huanqiu.com

baseurl=http://192.168.21.188/centos/$releasever/centosplus/$basearch/

#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus

gpgcheck=1

enabled=0

gpgkey=http://192.168.21.188/centos/RPM-GPG-KEY-CentOS-6

#contrib - packages by Centos Users

[contrib]

name=CentOS-$releasever - Contrib - huanqiu.com

baseurl=http://192.168.21.188/centos/$releasever/contrib/$basearch/

#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=contrib

gpgcheck=1

enabled=0

gpgkey=http://192.168.21.188/centos/RPM-GPG-KEY-CentOS-6

:wq! #保存退出

#########################

CentOS 7.x系列：

vi /etc/yum.repos.d/CentOS-Base.repo #添加以下代码

# CentOS-Base.repo

#

# The mirror system uses the connecting IP address of the client and the

# update status of each mirror to pick mirrors that are updated to and

# geographically close to the client. You should use this for CentOS updates

# unless you are manually picking other mirrors.

#

# If the mirrorlist= does not work for you, as a fall back you can try the

# remarked out baseurl= line instead.

#

#

[base]

name=CentOS-$releasever - Base

#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os

baseurl=http://192.168.21.188/centos/$releasever/os/$basearch/

gpgcheck=1

gpgkey=http://192.168.21.188/centos/RPM-GPG-KEY-CentOS-7

#released updates

[updates]

name=CentOS-$releasever - Updates

#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates

baseurl=http://192.168.21.188/centos/$releasever/updates/$basearch/

gpgcheck=1

gpgkey=http://192.168.21.188/centos/RPM-GPG-KEY-CentOS-7

#additional packages that may be useful

[extras]

name=CentOS-$releasever - Extras

#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras

baseurl=http://192.168.21.188/centos/$releasever/extras/$basearch/

gpgcheck=1

gpgkey=http://192.168.21.188/centos/RPM-GPG-KEY-CentOS-7

#additional packages that extend functionality of existing packages

[centosplus]

name=CentOS-$releasever - Plus

#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus

baseurl=http://192.168.21.188/centos/$releasever/centosplus/$basearch/

gpgcheck=1

enabled=0

gpgkey=http://192.168.21.188/centos/RPM-GPG-KEY-CentOS-7

:wq! #保存退出

#########################

或者参考：https://lug.ustc.edu.cn/wiki/mirrors/help/centos

把里面的http://mirrors.ustc.edu.cn/替换为http://192.168.21.188/

2、rpmforge源：

CentOS 5.x系列：

vi /etc/yum.repos.d/rpmforge.repo #添加以下代码

[rpmforge]

name = RHEL $releasever - RPMforge.net - dag

baseurl = http://192.168.21.188/repoforge/redhat/el5/en/$basearch/rpmforge

enabled = 1

protect = 0

gpgkey=http://192.168.21.188/repoforge/RPM-GPG-KEY-rpmforge

gpgcheck = 1

[rpmforge-extras]

name = RHEL $releasever - RPMforge.net - extras

baseurl = http://192.168.21.188/repoforge/redhat/el5/en/$basearch/extras

enabled = 0

protect = 0

gpgkey=http://192.168.21.188/repoforge/RPM-GPG-KEY-rpmforge

gpgcheck = 1

[rpmforge-testing]

name = RHEL $releasever - RPMforge.net - testing

baseurl = http://192.168.21.188/repoforge/redhat/el5/en/$basearch/testing

enabled = 0

protect = 0

gpgkey=http://192.168.21.188/repoforge/RPM-GPG-KEY-rpmforge

gpgcheck = 1

:wq! #保存退出

#########################

系统运维  www.osyunwei.com  温馨提醒：qihang01原创内容©版权所有,转载请注明出处及原文链

CentOS 6.x系列：

vi /etc/yum.repos.d/rpmforge.repo #添加以下代码

[rpmforge]

name = RHEL $releasever - RPMforge.net - dag

baseurl = http://192.168.21.188/repoforge/redhat/el6/en/$basearch/rpmforge

enabled = 1

protect = 0

gpgkey=http://192.168.21.188/repoforge/RPM-GPG-KEY-rpmforge

gpgcheck = 1

[rpmforge-extras]

name = RHEL $releasever - RPMforge.net - extras

baseurl = http://192.168.21.188/repoforge/redhat/el6/en/$basearch/extras

enabled = 0

protect = 0

gpgkey=http://192.168.21.188/repoforge/RPM-GPG-KEY-rpmforge

gpgcheck = 1

[rpmforge-testing]

name = RHEL $releasever - RPMforge.net - testing

baseurl = http://192.168.21.188/repoforge/redhat/el6/en/$basearch/testing

enabled = 0

protect = 0

gpgkey=http://192.168.21.188/repoforge/RPM-GPG-KEY-rpmforge

gpgcheck = 1

:wq! #保存退出

#########################

CentOS 7.x系列：

vi /etc/yum.repos.d/rpmforge.repo #添加以下代码

[rpmforge]

name = RHEL $releasever - RPMforge.net - dag

baseurl = http://192.168.21.188/repoforge/redhat/el7/en/$basearch/rpmforge

enabled = 1

protect = 0

gpgkey=http://192.168.21.188/repoforge/RPM-GPG-KEY-rpmforge

gpgcheck = 1

[rpmforge-extras]

name = RHEL $releasever - RPMforge.net - extras

baseurl = http://192.168.21.188/repoforge/redhat/el7/en/$basearch/extras

enabled = 0

protect = 0

gpgkey=http://192.168.21.188/repoforge/RPM-GPG-KEY-rpmforge

gpgcheck = 1

[rpmforge-testing]

name = RHEL $releasever - RPMforge.net - testing

baseurl = http://192.168.21.188/repoforge/redhat/el7/en/$basearch/testing

enabled = 0

protect = 0

gpgkey=http://192.168.21.188/repoforge/RPM-GPG-KEY-rpmforge

gpgcheck = 1

:wq! #保存退出

#########################

3、epel源：

CentOS 5.x系列：

vi /etc/yum.repos.d/epel.repo #添加以下代码

[epel]

name=Extra Packages for Enterprise Linux 5 - $basearch

baseurl=http://192.168.21.188/epel/5/$basearch

failovermethod=priority

enabled=1

gpgcheck=1

gpgkey =http://192.168.21.188/epel/RPM-GPG-KEY-EPEL-5

[epel-debuginfo]

name=Extra Packages for Enterprise Linux 5 - $basearch - Debug

baseurl=http://192.168.21.188/epel/5/$basearch/debug

failovermethod=priority

enabled=0

gpgkey =http://192.168.21.188/epel/RPM-GPG-KEY-EPEL-5

gpgcheck=1

[epel-source]

name=Extra Packages for Enterprise Linux 5 - $basearch - Source

baseurl=http://192.168.21.188/epel/5/SRPMS

failovermethod=priority

enabled=0

gpgkey =http://192.168.21.188/epel/RPM-GPG-KEY-EPEL-5

gpgcheck=1

:wq! #保存退出

#########################

CentOS 6.x系列：

vi /etc/yum.repos.d/epel.repo #添加以下代码

[epel]

name=Extra Packages for Enterprise Linux 6 - $basearch

baseurl=http://192.168.21.188/epel/6/$basearch

failovermethod=priority

enabled=1

gpgcheck=1

gpgkey =http://192.168.21.188/epel/RPM-GPG-KEY-EPEL-6

[epel-debuginfo]

name=Extra Packages for Enterprise Linux 6 - $basearch - Debug

baseurl=http://192.168.21.188/epel/6/$basearch/debug

failovermethod=priority

enabled=0

gpgkey =http://192.168.21.188/epel/RPM-GPG-KEY-EPEL-6

gpgcheck=1

[epel-source]

name=Extra Packages for Enterprise Linux 6 - $basearch - Source

baseurl=http://192.168.21.188/epel/6/SRPMS

failovermethod=priority

enabled=0

gpgkey =http://192.168.21.188/epel/RPM-GPG-KEY-EPEL-6

gpgcheck=1

:wq! #保存退出

#########################

CentOS 7.x系列：

vi /etc/yum.repos.d/epel.repo #添加以下代码

[epel]

name=Extra Packages for Enterprise Linux 7 - $basearch

baseurl=http://192.168.21.188/epel/beta/7/$basearch

failovermethod=priority

enabled=1

gpgcheck=1

gpgkey =http://192.168.21.188/epel/RPM-GPG-KEY-EPEL-7

[epel-debuginfo]

name=Extra Packages for Enterprise Linux 7 - $basearch - Debug

baseurl=http://192.168.21.188/epel/beta/7/$basearch/debug

failovermethod=priority

enabled=0

gpgkey =http://192.168.21.188/epel/RPM-GPG-KEY-EPEL-7

gpgcheck=1

[epel-source]

name=Extra Packages for Enterprise Linux 7 - $basearch - Source

baseurl=http://192.168.21.188/epel/beta/7/SRPMS

failovermethod=priority

enabled=0

gpgkey =http://192.168.21.188/epel/RPM-GPG-KEY-EPEL-7

gpgcheck=1

:wq! #保存退出

#########################

四、测试yum源是否配置正确

1、当前系统版本为CentOS 6.x，以此为例

cd /etc/yum.repos.d/

mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup

vi CentOS-Base.repo #添加以下代码

# CentOS-Base.repo

#

# The mirror system uses the connecting IP address of the client and the

# update status of each mirror to pick mirrors that are updated to and

# geographically close to the client. You should use this for CentOS updates

# unless you are manually picking other mirrors.

#

# If the mirrorlist= does not work for you, as a fall back you can try the

# remarked out baseurl= line instead.

#

#

[base]

name=CentOS-$releasever - Base - huanqiu.com

baseurl=http://192.168.21.188/centos/$releasever/os/$basearch/

#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os

gpgcheck=1

gpgkey=http://192.168.21.188/centos/RPM-GPG-KEY-CentOS-6

#released updates

[updates]

name=CentOS-$releasever - Updates - huanqiu.com

baseurl=http://192.168.21.188/centos/$releasever/updates/$basearch/

#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates

gpgcheck=1

gpgkey=http://192.168.21.188/centos/RPM-GPG-KEY-CentOS-6

#additional packages that may be useful

[extras]

name=CentOS-$releasever - Extras - huanqiu.com

baseurl=http://192.168.21.188/centos/$releasever/extras/$basearch/

#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras

gpgcheck=1

gpgkey=http://192.168.21.188/centos/RPM-GPG-KEY-CentOS-6

#additional packages that extend functionality of existing packages

[centosplus]

name=CentOS-$releasever - Plus - huanqiu.com

baseurl=http://192.168.21.188/centos/$releasever/centosplus/$basearch/

#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus

gpgcheck=1

enabled=0

gpgkey=http://192.168.21.188/centos/RPM-GPG-KEY-CentOS-6

#contrib - packages by Centos Users

[contrib]

name=CentOS-$releasever - Contrib - huanqiu.com

baseurl=http://192.168.21.188/centos/$releasever/contrib/$basearch/

#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=contrib

gpgcheck=1

enabled=0

gpgkey=http://192.168.21.188/centos/RPM-GPG-KEY-CentOS-6

:wq! #保存退出

#########################

vi rpmforge.repo #添加以下代码

[rpmforge]

name = RHEL $releasever - RPMforge.net - dag

baseurl = http://192.168.21.188/repoforge/redhat/el6/en/$basearch/rpmforge

enabled = 1

protect = 0

gpgkey=http://192.168.21.188/repoforge/RPM-GPG-KEY-rpmforge

gpgcheck = 1

[rpmforge-extras]

name = RHEL $releasever - RPMforge.net - extras

baseurl = http://192.168.21.188/repoforge/redhat/el6/en/$basearch/extras

enabled = 0

protect = 0

gpgkey=http://192.168.21.188/repoforge/RPM-GPG-KEY-rpmforge

gpgcheck = 1

[rpmforge-testing]

name = RHEL $releasever - RPMforge.net - testing

baseurl = http://192.168.21.188/repoforge/redhat/el6/en/$basearch/testing

enabled = 0

protect = 0

gpgkey=http://192.168.21.188/repoforge/RPM-GPG-KEY-rpmforge

gpgcheck = 1

:wq! #保存退出

#########################

vi epel.repo #添加以下代码

[epel]

name=Extra Packages for Enterprise Linux 6 - $basearch

baseurl=http://192.168.21.188/epel/6/$basearch

failovermethod=priority

enabled=1

gpgcheck=1

gpgkey =http://192.168.21.188/epel/RPM-GPG-KEY-EPEL-6

[epel-debuginfo]

name=Extra Packages for Enterprise Linux 6 - $basearch - Debug

baseurl=http://192.168.21.188/epel/6/$basearch/debug

failovermethod=priority

enabled=0

gpgkey =http://192.168.21.188/epel/RPM-GPG-KEY-EPEL-6

gpgcheck=1

[epel-source]

name=Extra Packages for Enterprise Linux 6 - $basearch - Source

baseurl=http://192.168.21.188/epel/6/SRPMS

failovermethod=priority

enabled=0

gpgkey =http://192.168.21.188/epel/RPM-GPG-KEY-EPEL-6

gpgcheck=1

:wq! #保存退出
