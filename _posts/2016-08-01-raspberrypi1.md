---
layout: single
title:  "树莓派笔记:1"
date:   2016-08-01
categories: blog
tag: tech
---
![newsetup](/images/pi.jpg)
今天终于收到了计划买很久的树莓派3，卡片大小的便携式电脑。之所以不将其称之为开发板一类的，是因为它1.2GHz四核 1G ram的运算能力，放在几年前，真的好像是一台家用电脑。

## 组装

从淘宝下单的同时一并买了树莓派的外壳，以及电源和sd卡。外壳的作用主要是为了保护裸露的电路板。micro sd卡对于树莓派来说相当于硬盘，而电源则是因为官方配置里真的是只有一块单板，供电需要5v2A的micro USB供电。

在装上外壳之前要记得在单板的CPU GPU上贴上散热片，这样可以方便散热。

## 启动

首次启动使用HDMI链接到显示器，同时链接USB键盘鼠标。一切顺利，系统盘可以去官方下载烧录在SD卡中，运行内核为

	Linux raspberrypi 4.1.18-v7+ #846 SMP Thu Feb 25 14:22:53 GMT 2016 armv7l GNU/Linux

所以这是一个完整版的linux

## 控制

因为习惯于SSH登录linux的方式，也就没有花心思去搞vnc viewer。树莓派自带ssh服务，系统默认开启，在家用路由器中配置一条静态dhcp就可以将派绑定一个固定的ip地址，这样便可以ssh访问pi了。

但是，在这个时候与到了一个问题。
* 拔掉USB键盘改用SSH控制派之后变得极其不稳定，无缘无故的就会失去链接。
* ssh显示host is down，ping也无法ping通pi。
* 基本将问题定位为wifi问题，将pi换到一个距离路由器很近的地方iwconfig查看信号质量基本是满的，问题依然没有改善。
* 一番搜索之后发现问题应该是wifi在没有数据时自动进入了休眠状态。应该在设置处关闭wifi电源管理。
* 修改配置文件，在/etc/network/interfaces中 wlan0前添加

		post-up iw dev $IFACE set power_save off

重启树莓派。 问题搞定。

终于可以安心使用这部迷你主机了。
