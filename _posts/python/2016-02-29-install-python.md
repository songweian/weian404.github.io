---
layout: post
title: install python
category: python
tags: python
keywords: python linux
description: install python env

---
### install python for CentOS [doc](https://www.python.org/doc/)
```
yum install  -y gcc make zlib-devel openssl openssl-devel
wget https://www.python.org/ftp/python/3.5.1/Python-3.5.1.tgz
tar -xzvf Python-3.5.1.tgz
cd Python-3.5.1
./configure --prefix=/usr/bin/python3.5.1
make
make install
```
### install pip [doc](https://pip.pypa.io/en/stable/installing/)

```
wget https://bootstrap.pypa.io/get-pip.py
python get-pip.py
```

### install virtualenv [doc](https://virtualenv.readthedocs.org/en/latest/installation.html)
```
pip install virtualenv
```
### install virtualenvwrapper [doc](https://virtualenvwrapper.readthedocs.org/en/latest/)
```
pip install virtualenvwrapper
```
