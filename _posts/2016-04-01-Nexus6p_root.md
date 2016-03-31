---
layout: post
title: "nexus6p 刷机"
date: 2016-04-01 02:12
categories: [ROM]
tags: [tools]
---
###Nexus6p刷机 
~生命在于运动，手机在于刷机~ 
#####一、解锁
* 下载好adb和fastboot[驱动](http://pan.baidu.com/s/1i376T5z)，解压到指定目录下
* 手机打开“开发者选项”，勾选usb调试并且选择MTP方式连接手机
* 管理员身份打开命令提示符，到解压的目录执行
``` shell
adb reboot bootloader
```
* 这时手机重启会进入bootloader，继续输入
``` shell
fastboot flashing unlock
```
* 用音量+/-选择确认，重启之后解锁完成
#####二、在线刷机
^未完待续……^
