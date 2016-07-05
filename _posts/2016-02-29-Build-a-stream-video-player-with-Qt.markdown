---
layout: single
title:  "用Qt写一个流媒体播放器"
date:   2016-02-29
categories: blog
---



![demo](/images/20160229122845.png)

## 前提

Qt内置了多媒体播放的字库QMultiMedia，使用这个组件内提供的各种工具可以比较方便的写出一个简单的播放器，但是这个组件没有提供流媒体播放
的功能。所以如果想支持udp或rtp的流媒体类型播放，需要使用第三方库进行开发。

比较流行的视频第三方库主要有ffmpeg和vlc两个，可以找到使用Qt+ffmpeg开发的播放器lib QtAV。但是在使用时个人选择了vlc主要是因为vlc-qt这个库的存在使得在Qt环境下用libvlc开发变得及其方便。


## 编码

使用Qt+vlc开发首先需要下载安装第三方库，这里也就是：

  * libvlc

  下载地址：ftp://ftp.videolan.org/pub/videolan/vlc/2.2.2/

  * vlc-qt

  下载地址： https://vlc-qt.tano.si/

这里有一点需要注意的就是最新版本的vlc-qt是基于libvlc2.2.2一集qt5.5.1环境开发的，所以需要搭配对应版本的开发环境使用，如果环境配置不对的话虽然可以通过编译但是程序运行时会在创建vlcinstance的时候crash。

得到第三方库之后首先需要将其添加到Qt的开发环境中。以Qtcreator为例可以在project上右键选择add library选择ext lib然后选择vlc-qt以及libvlc的对应目录进行添加。

添加完后会在.pro文件中自动生成如下语句。

    win32:CONFIG(release, debug|release): LIBS += -L$$PWD/../../../../../Qt/VLC-Qt_1.0.1_win32_mingw/lib/ -lVLCQtCore -lVLCQtWidgets
    else:win32:CONFIG(debug, debug|release): LIBS += -L$$PWD/../../../../../Qt/VLC-Qt_1.0.1_win32_mingw/lib/ -lVLCQtCored -lVLCQtWidgetsd
    else:unix: LIBS += -L$$PWD/../../../../../Qt/VLC-Qt_1.0.1_win32_mingw/lib/ -lVLCQtCore -lVLCQtWidgets

    INCLUDEPATH += $$PWD/../../../../../Qt/VLC-Qt_1.0.1_win32_mingw/include
    DEPENDPATH += $$PWD/../../../../../Qt/VLC-Qt_1.0.1_win32_mingw/include


使用vlc-qt相关工具需要在文件中引入响应的头文件。因为我使用c++开发所以不需要添加VLCQtQml这个lib。在项目文件中加入头文件：

    #include "VLCQtCore/Common.h"
    #include "VLCQtCore/Media.h"
    #include "VLCQtCore/MediaListPlayer.h"
    #include "VLCQtCore/MediaPlayer.h"
    #include "VLCQtCore/Instance.h"
    #include "VLCQtWidgets/WidgetVideo.h"


在mainwindow中添加播放窗口：

    MainWindow::MainWindow(QWidget *parent) :
    QMainWindow(parent),_media(0),_videowidget(0)
    {
      _instance = new VlcInstance(VlcCommon::args(), parent); //构造vlcinstance
      QString url = "udp/h264://@147.8.179.120:6666";
      _media = new VlcMedia(url, _instance);    //构造播放媒体
      _player = new VlcMediaPlayer(_instance);  // 构造播放器
      _videowidget = new VlcWidgetVideo(_player,this);  //构造显示widget
      _player->setVideoWidget(_videowidget);
      _videowidget->show();
      _player->open(_media);
      _player->play();
      setCentralWidget(_videowidget);
      setSizeIncrement(500,500);
    }


整体流程可以理解为：

  1. 创建vlcinstance，理解为创建一个vlc对象，相当于一个播放器核心。
  2. 构造Vlcmediaplayer，工作于instance之上的一个播放器用于播放视频。
  3. 构造vlcwidgetvideo，用于显示vlcplayer内容的窗口组件。

这里有几个比较容易掉坑的点，着重纪录一下：

  1. 构造vlcinstance的时候parent参数位置很多网上的资料是以this传入，但是这样在运行的时候会提示父子不在同一个现成中从而运行失败。参考了vlc-qt开发者在Github上的回应（https://github.com/vlc-qt/vlc-qt/issues/87）这里应该传入parent为参数。

  2. 在打开一个流媒体文件时，因为流媒体编码的方式不同，vlc无法在默认情况下打开一个h264编码的流，所以需要在url中做一个说明告诉vlc的选择h264的demuxer，同时因为vlc的url默认使用ipv6所以需要在ipv4地址前加一个@符号进行地址兼容。

## 最后

在完成以上步骤之后代码可以正常编译通过，但是如果想要运行需要将对应的dll文件拷贝到qt项目的编译目录下。这里对应的就是vlc-qt解压目录下bin子目录下的dll以及lib-vlc解压目录下的dll以及plugins文件夹。

完成后可以正确编译运行，可以使用ffmpeg在另一台机器中向本机发送udp视频流，目的地址为本机：

    ffmpeg -i UNIQLO_MIXPLAY.flv -v 0 -vcodec mpeg4 -f mpegts udp:147.8.179.120:6666

现在就可以在本机看到对方发来的视频流了。
