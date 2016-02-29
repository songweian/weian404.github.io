---
layout: post
title: svn 迁移到 git
category: git
tags: git
keywords:
description:
---
从 svn 迁移到 gitlab

找出所有提交者

```
$ svn log --xml | grep author | sort -u | perl -pe 's/.>(.?)<./$1 = /'
```
手动设置对应关系 users.txt

```
suqian.yf = 苏千 <suqian.yf@taobao.com>
...
zhangxing_z.pt = 章星 <zhangxing_z.pt@taobao.com>
zhenghaibo.pt = 郑海波 <zhenghaibo.pt@taobao.com>
```
从svn clone 出来

```
$ git svn clone http://svnurl.com/weibo/trunk --authors-file=users.txt --no-metadata weibo
```
在gitlab上创建tcif项目，并添加到remotes

```
$ git remote add origin git@gitlab.abc.com:weibo.git
```
push 到 gitlab

```
$ git push origin --all
```
