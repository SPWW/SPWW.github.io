---
layout: single
title:  "树莓派笔记:3"
date:   2016-09-01
categories: blog
---

![slackpi](http://treelineinteractive.com/blog/wp-content/uploads/2015/12/slack_and_raspberry_pi.jpg)

## 树莓派+slack快速搭建远程控制平台

相信对于很多人来说，树莓派的最大的优势就是功耗低，可以随时保持开机，作为家庭小型服务器再适合不过了。但是有一个问题就，树莓派是可以长期待机，但是如何在家外面访问到隐藏在内网中的树莓派呢？

看过网上很多的教程，基本介绍都是使用花生壳一类的内网穿透工具，但是如果并不需要复杂应用，只是想通过树莓派监控一下家庭物联网状况，又或者想命令树莓派开启或者关闭一些简单的任务的话，使用内网穿透工具就显得没有什么必要了。

在这里介绍一个我目前在使用的方法，通过slack集成的方式，讲树莓派作为一个slackbot接入项目，这样就可以像普通聊天一样对pi发送指令了。而为什么slack可以跨过防火墙网关找到并没有公网ip的树莓派，这里就要感谢websocket技术，salckbot的客户端程序与slack服务器之间保持了一条websocket长连接，这样就可以做到服务器主动推送任务了。相信很多穿透平台也是用类似的技术实现的。

说道这里可能很多人还不知道slack是什么，在这摘抄一段wiki，有兴趣的话可以自己研究下，对于团队协作，以及系统集成自动化都会有不少的帮助：

  Slack is a cloud-based team collaboration tool co-founded by Stewart Butterfield, Eric Costello,Cal Henderson, and Serguei Mourachov.[1](#) Slack began as an internal tool used by their company, Tiny Speck, in the development of Glitch, a now defunct online game.

关于slackbot如何构建，最简单的方式是使用python的slackbot第三方库，可以通过装饰器语法简单的定义slackbot的action。python slackbot的github地址在此。[https://github.com/lins05/slackbot](https://github.com/lins05/slackbot)

安装好之后只需要去slack项目中创建slackbot，将token设置到client中就可以方便的远程控制树莓派了。
