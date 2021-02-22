---
layout: post
title: "在第三方源中加入自己的源"
excerpt: "如果主源被锁定无法修改，可以再sources.list.d文件夹中加入自己的源."
categories: [sources.list]
comments: true
---

# Ubuntu中/etc/apt/sources.list.d目录的作用

在Ubuntu下软件源的文件是/etc/apt/sources.list，那么sourdces.list.d目录下的文件又是什么作用呢？

该文件夹下的文件是第三方软件的源，可以分别存放不同的第三源地址，只需“扩展名”为list即可，更新后使用命令：
```
apt update
```
更新一下软件源。

在某些情况下主源被锁定，又想添加想要的源就可以使用下面的方法。附上一个实用源，cp到/etc/apt/sources/list.d/下，apt update即可。

```
#deb cdrom:[Ubuntu-Kylin 20.04.1 LTS _Focal Fossa_ - Release amd64 (20200731)]/ focal main multiverse restricted universe

# See http://help.ubuntu.com/community/UpgradeNotes for how to upgrade to
# newer versions of the distribution.
deb http://mirrors.aliyun.com/ubuntu/ focal main restricted
deb-src http://mirrors.aliyun.com/ubuntu/ focal main restricted

## Major bug fix updates produced after the final release of the
## distribution.
deb http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted
deb-src http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted

## N.B. software from this repository is ENTIRELY UNSUPPORTED by the Ubuntu
## team. Also, please note that software in universe WILL NOT receive any
## review or updates from the Ubuntu security team.
deb http://mirrors.aliyun.com/ubuntu/ focal universe
deb-src http://mirrors.aliyun.com/ubuntu/ focal universe
deb http://mirrors.aliyun.com/ubuntu/ focal-updates universe
deb-src http://mirrors.aliyun.com/ubuntu/ focal-updates universe

## N.B. software from this repository is ENTIRELY UNSUPPORTED by the Ubuntu 
## team, and may not be under a free licence. Please satisfy yourself as to 
## your rights to use the software. Also, please note that software in 
## multiverse WILL NOT receive any review or updates from the Ubuntu
## security team.
deb http://mirrors.aliyun.com/ubuntu/ focal multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal multiverse
deb http://mirrors.aliyun.com/ubuntu/ focal-updates multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-updates multiverse

## N.B. software from this repository may not have been tested as
## extensively as that contained in the main release, although it includes
## newer versions of some applications which may provide useful features.
## Also, please note that software in backports WILL NOT receive any review
## or updates from the Ubuntu security team.
deb http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse

## Uncomment the following two lines to add software from Canonical's
## 'partner' repository.
## This software is not part of Ubuntu, but is offered by Canonical and the
## respective vendors as a service to Ubuntu users.
deb http://archive.canonical.com/ubuntu focal partner
deb-src http://archive.canonical.com/ubuntu focal partner

deb http://mirrors.aliyun.com/ubuntu focal-security main restricted
deb-src http://mirrors.aliyun.com/ubuntu focal-security main restricted
deb http://mirrors.aliyun.com/ubuntu focal-security universe
deb-src http://mirrors.aliyun.com/ubuntu focal-security universe
deb http://mirrors.aliyun.com/ubuntu focal-security multiverse
deb-src http://mirrors.aliyun.com/ubuntu focal-security multiverse

# This system was installed using small removable media
# (e.g. netinst, live or single CD). The matching "deb cdrom"
# entries were disabled at the end of the installation process.
# For information about how to configure apt package sources,
# see the sources.list(5) manual.
```