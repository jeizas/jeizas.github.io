---
layout: post
title: "archlinux 安装中遇到的问题"
date: 2016-04-26 00:20
categories: [skill]
tags: [tools]
---

#### 一、安装yaourt

> 琐碎小东西记录下来，省得每次都得搜索

1. 编辑/etc/hosts文件

```shell
//配置/etc/pacman.conf
[archlinuxfr]
SigLevel = Never
Server = http://repo.archlinux.fr/x86_64
//更新源，安装yaourt
pacman -Sy
pacman -S yaourt
```

2. 安装网卡驱动，wifi连网

```shell
//安装网络工具
pacman -S net-tools dnsutils inetutils iproute2
//禁用dhcpcd，启动NetworkMamager管理网络
sudo systemctl disable dhcpcd
sudo systemctl restart NetworkManager
//安装网卡驱动
yaourt -S broadcom-wl-dkms
depmod -a
//安装连接wifi输入密码的panel
sudo pamcan -S gnome-keyring
```
