---
layout: single
title:  "树莓派笔记:2"
date:   2016-08-02
categories: blog
---

** 第一个自动化程序 **
树莓派最大的好处是什么？我认为是小巧低功耗，这也就意味着耗电少无风扇无噪音可以长期开机。因此，树莓派最大的作用就是可以用来执行一些服务程序，通常具有这样的特点：
* 轻量级
* 基于时间自动启动
* io自动化
因此，今天下午感谢台风放假，有时间写出第一个pi上的自动化脚本：自动生日祝福。

## 需求
基于sql数据库中的信息，查找当天生日的成员，并向成员注册邮箱发送生日祝福。

## 实现
### 数据库
使用关系型数据库，这样可以用sql方便的操作，也方便于后期数据更新与管理。这里使用sqlite，因为请量化以及本地化的原因，足够满足应用需要。

### 脚本
第一步从数据库中录出当天生日的成员名单，这里需要用到sql的通配符。语句如下：
c.execute("select id,name,email,birthday from members where birthday like "%"+datetimeobj.strftime('%m-%d')+"";")

使用python datetime 模块得到当天日期，经过格式化之后进行数据查找。

第二部利用python stmp 和 email两个模块生成一封生日祝福邮件：具体做法直接参照之前的[自动化邮件脚本](https://github.com/SPWW/dummycodes/tree/master/send-email-via-python)。

### 定时启动程序
在linux系统下可以利用crontab进行程序定时启动操作，参考了网上很多教程，感觉写的都过于繁琐，这里简单介绍crontab的使用。需要记住的命令只有两个：
1. crontab -l 查看当前的定时任务
2. crontab -e 编辑crontab的定时任务列表

首先在第一次执行crontab -e的时候回需要你选择一个editor进行编辑，这里可以随便选择一个顺手的。然后文件的内容以行为单位，一行代表一个执行任务。注释以\\#开头，注释与语句不能放在同一行。执行语句的语法如下

 \* \* \* \* \* \* command

前面六个\*分别代表，分 小时 日 月 年 星期。
command为shell command
前面的时间信息摄制为\*代表在这一项上通配，可以理解为“每”的意思。例如：
8 \* \* \* command

代表每天八点执行，command。

好了，至此，已经可以完成第一个跑在pi上的时间任务了。
