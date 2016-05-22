---
layout: post
title:  "Windows远程桌面编写运行OpenGL程序"
date:   2016-05-17 23:06:05
categories: Basics
tags: 网速 OpenGL VNC
---
* content
{:toc} 


# 前言
实验室刚搬到原来308教室改造的屋子，虽然桌椅都换成新的了，但是却坐满了几个学院的人。每个人的电脑几乎都不关，虽然我用着小米电风扇还有点作用，但是吹来的几乎都是热风。在实验室异常烦躁，呆了几天啥成果都没有，果断换地。因为学习资料以及代码都在实验室的台式机上，又不方便携带，所以首先想到的是远程桌面控制实验室的电脑。即使网络不大好，勉强撑到空调供冷就好了。







# 问题
## 网速 ##
- 在图书馆的无线网络速度都有72Mbps了，但**实验室有线宽带网络速度带宽仅为10Mbps**

![笔记本无线网速度](http://upload-images.jianshu.io/upload_images/1242974-2e594b08aec5333d.png)

## 显示 ##

- **Windows7自带的远程桌面连接不能显示程序中绘制的图形**

 在调用远程桌面连接程序时，使用`Win键+R`快捷键调用运行，输入`mstsc`，回车即可出现下面的窗口。然后再输入远程连接电脑的`IP`，这里我已经设置了最高性能了。
 

![Windows7远程桌面连接](http://upload-images.jianshu.io/upload_images/1242974-f19729fc24b683b0.png)
在进入我的远程电脑，运行程序时，尴尬的情况出现了-远程桌面无法显示`OpenGL`程序，如下图：

![未显示3D图形](http://upload-images.jianshu.io/upload_images/1242974-c0eebdf9146f4a61.png)

 

# 解决 #

## 网速 ##
其实之前我的电脑一直分配不到ip，在以前也会遇到这种情况，有同学关掉电脑，过段时间就会分配到ip，我又可以连上网了。但是这次我是连续两天都没有连上网，然后折腾了老半天更新驱动，最后连上网了，就是前述的10Mbps的网络。后来到图书馆，远程连接实在是太卡了。我现在急着写东西，实验室电脑的网速，自然让我想到了《疯狂动物城》的树懒。吃过晚饭后，到实验室**把电脑上的网线换掉，然后网速就飞起来了。**

## 显示 ##

在[OpenGL的官方论坛](https://www.opengl.org/discussion_boards/showthread.php/164372-Remote-Desktop)上查到了`windows`远程桌面连接支持的问题，由于远程桌面仅提供通用的`GDI+`，可以运行`OpenGL 1.1`，但对高版本的程序需要**安装客户端驱动支持。**

> 使用win7的远程桌面控制另一台支持opengl3.3的计算机，结果报告我的显卡不支持。只好找了个别的软件
**Remote Desktop Control** 。效果不错

我下载使用过这个软件，但是效果不大好，最后选择下载同类型的**`RealVNC`**，不仅是因为这个软件显示效果好，而且还提供在移动客户端的友好支持。手机、笔记本都可以访问我实验室的电脑啦！简直吊炸天！

![移动端显示效果](http://upload-images.jianshu.io/upload_images/1242974-ea5dd5281d519931.jpg)

在移动端还提供了多指触控，虚拟键盘等功能，坐在地铁上都可以运行下程序，调试个BUG，想想都觉得妙不可言！


 
![PC端显示效果](http://upload-images.jianshu.io/upload_images/1242974-16feedd448637e6e.png)

**VNC**（Virtual Network Computing），为一种使用[RFB协议](https://zh.wikipedia.org/w/index.php?title=RFB%E5%8D%94%E5%AE%9A&action=edit&redlink=1)的屏幕画面分享及远程操作软件。此软件借由网络，可发送键盘与鼠标的动作及即时的屏幕画面。VNC与操作系统无关，因此可跨平台使用，例如可用[Windows](https://zh.wikipedia.org/wiki/Windows)连接到某[Linux](https://zh.wikipedia.org/wiki/Linux)的电脑，反之亦同。甚至在没有安装客户端程序的电脑中，只要有支持[JAVA](https://zh.wikipedia.org/wiki/JAVA)的[浏览器](https://zh.wikipedia.org/wiki/%E7%80%8F%E8%A6%BD%E5%99%A8)，也可使用。

### VNC软件
由于VNC以GPL授权，派生出了几个VNC软件：

- [RealVNC](https://zh.wikipedia.org/w/index.php?title=RealVNC&action=edit&redlink=1)：由VNC团队部分成员开发，分为全功能商业版及免费版。
- [TightVNC](https://zh.wikipedia.org/w/index.php?title=TightVNC&action=edit&redlink=1)：强调节省带宽使用。
- [UltraVNC](https://zh.wikipedia.org/w/index.php?title=UltraVNC&action=edit&redlink=1)：加入了TightVNC的部分程序及加强性能的图型映射驱动程序，并结合Active Directory及NTLM的账号密码认证，但仅有Windows版本。
- [Vine Viewer](https://zh.wikipedia.org/w/index.php?title=Vine_Viewer&action=edit&redlink=1)：MacOSX的VNC客户端。

这些软件各有所长，例如UltraVNC支持文件传输以及全屏模式。而这些软件间大多遵循基本的VNC协议，因此大多可互通使用。


### 下载安装

在RealVNC官方网站提供了多种平台下的安装包。
 
- [官网下载页面](http://www.realvnc.com/download/viewer/)

![RealVNC](http://upload-images.jianshu.io/upload_images/1242974-aef4068853ab65d3.png)
    
之前是搜注册码的，搜到了这个网站，里面的注册码可以使用的，并且还提供了相应版本的下载链接地址，感觉做的很用心，推荐给大家。

- [iHackSoft下载页面](http://www.ihacksoft.com/vnc-realvnc-5.html)

![iHackSoft](http://upload-images.jianshu.io/upload_images/1242974-55320545fc0b2171.png)


- 以上内容纯属瞎扯，其实我最想记下来的是下面的注册码，哈哈


```
    BQ24G-PDXE4-KKKRS-WBHZE-F5RCA
    BQ24G-PDXE4-KKKRS-WBHZE-F5RCA
    8ZEZH-QPANM-NX3A5-8C4TS-8B97A
    7AB4X-3YNXF-C5MRR-59DJG-7HGNA
    UPL8P-CN2MT-85ERA-N3E3B-GERDA
    X24XP-V247M-HEMNV-ZTMZK-FNB7A
```

OVER.