---
layout: post
title: install gitlab
category: git
tags: git
keywords:
description:
---


```
https://about.gitlab.com/downloads/Install a GitLab CE Omnibus package on
```
CentOS 6 (and RedHat/Oracle/Scientific Linux 6)
 Check if your server meets the hardware requirements

1. Install and configure the necessary dependencies

If you install Postfix to send email please select ‘Internet Site’ during setup. Instead of using Postfix you can also use Sendmail or configure a custom SMTP server. Do not use Exim to send email from GitLab.

On Centos 6 and 7, the commands below will also open HTTP and SSH access in the system firewall.

```
sudo yum install openssh-server
sudo yum install postfix
sudo yum install cronie
sudo service postfix start
sudo chkconfig postfix on
sudo lokkit -s http -s ssh
```
2. Download the Omnibus package and install everything

```
curl -O https://downloads-packages.s3.amazonaws.com/centos-6.6/gitlab-7.9.2_omnibus-1.el6.x86_64.rpm
sudo rpm -i gitlab-7.9.2_omnibus-1.el6.x86_64.rpm
```

3. Configure and start GitLab

```
sudo gitlab-ctl reconfigure
4. Browse to the hostname and login
```

```
Username: root
Password: 5iveL!fe
```
