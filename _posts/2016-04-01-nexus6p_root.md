---
layout: post
title: "nexus6p 刷机"
date: 2016-04-01 02:12
categories: [ROM]
tags: [tools]
---
### Nexus6p刷机 
  > 生命在于运动，手机在于刷机 

##### 一、解锁
* 下载好adb和fastboot[驱动](http://pan.baidu.com/s/1i376T5z)，解压到指定目录下
* 手机打开“开发者选项”，勾选usb调试并且选择MTP方式连接手机
* 管理员身份打开命令提示符，到解压的目录执行

~~~ shell
adb reboot bootloader
~~~

* 这时手机重启会进入bootloader，继续输入

~~~ shell
fastboot flashing unlock
~~~

* 用音量+/-选择确认，重启之后解锁完成

##### 二、在线刷机
1. 下载[官方镜像](https://developers.google.com/android/nexus/images),解压到指定的文件夹，然后将adb和fastboot驱动也放入解压的文件夹中，等待刷机
2. shift+右键此文件夹，选择在此处打开命令窗口，输入adb reboot bootloader回车，这时手机会进入bootloader
3. 若保留数据：编辑flash-all.bat文件，删掉最后一行，update前的“-w”。运行flash-all.bat脚本，手机重启，刷机成功。

##### 三、手机ROOT

- 刷入第三方recovery，使用大神制作的[twrp](https://twrp.me)，将其放入任意目录，shift+右键刷机文件夹，选择在此处打开命令窗口

~~~ shell
adb reboot bootloader
~~~

- 回车，进入bootloader，输入

~~~ shell
fastboot flash recovery twrp-xxxx-angler.img
~~~

- 下载新版本的[superSU](http://download.chainfire.eu/supersu)，放入手机根目录，手机关机，按音量“-”和“电源键”，进入刷机模式，选择“install”，找到刚才的文件开始root。

##### 四、刷入刷pixel home以及圆角图标

- 下载[Pixel-Mod-7.1.1-N6P-nmf26f-chromloop.com.zip](https://forum.xda-developers.com/nexus-6p/themes-apps/mod-pixel-exclusive-home-button-t3499244)
- 安装flashfire(Play Store)
