---
layout: post
title: "阿里云主机名 修改"
date: 2016-04-25 13:20
categories: [skill]
tags: [tools]
---
#### 一、修改主机名

> 琐碎小东西记录下来，省得每次都得搜索

* 编辑/etc/hosts文件

```shell
vi /etc/hosts
```
10.129.34.222 主机名

* 编辑/etc/sysconfig/network文件
HOSTNAME=主机名

* Hostname设置

```shell
vi /etc/hostname
```
把主机名添加到文件里面

* reboot 重启
