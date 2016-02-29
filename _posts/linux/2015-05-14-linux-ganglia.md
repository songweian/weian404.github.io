---
layout: post
title: ganglia
category: linux
tags: linux
keywords: 
description: 
---
安装gmod：
libconfuse
	http://download.savannah.gnu.org/releases/confuse/confuse-2.7.tar.gz
	./configure CFLAGS=-fPIC --disable-nls && make && make install  
	 ./configure CFLAGS=-fPIC --disable-nls && make && make install
pkgconfig
	http://pkgconfig.freedesktop.org/releases/pkg-config-0.18.1.tar.gz
	http://pkgs.org/centos-6/centos-x86_64/pkgconfig-0.23-9.1.el6.x86_64.rpm.html
	
PCRE
	http://sourceforge.net/projects/pcre/files/pcre/
APR
	http://apr.apache.org/download.cgi
	   ./configure  PKG_CONFIG_PATH=/usr/local/apr/lib/pkgconfig
   make
   make install
	
	rpmbuild -tb apr-1.4.x.tar.bz2
libexpat
http://sourceforge.net/projects/expat/files/latest/download
zlib
http://sourceforge.net/projects/libpng/files/zlib/1.2.7/zlib-1.2.7.tar.gz/download?use_mirror=jaist&download=

http://www.linuxidc.com/Linux/2012-01/51663p3.htm
安装gmetad：
libconfuse
pkgconfig
PCRE
APR
RRDtool

安装gweb
Apache
PHP5.2+
PHP JSON扩展的安装和启用
