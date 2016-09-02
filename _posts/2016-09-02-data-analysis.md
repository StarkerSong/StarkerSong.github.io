---
layout: post
title: "基于scrapy-redis分布式网络爬虫存储数据分析（三）"
date: 2016-09-02 16:38:55 +0800
categories: scrapy-redis
tags: spider power-bi
---
* content
{:toc}
 

# 数据分析与展示 #


我懂（感觉好奇怪，人家还是很含蓄的，额，隔壁老王要喷我了，在他面前且叫你懂吧~）的每篇文章很有特色，每篇文章按主键自增，对应的URL都是唯一，所以我直接暴力爬取了全站的文章，但是这里为了快速加载数据只随机统计了部分爬取存入到MySQL中全部的文章数量。作为一名足球界的小菜鸟，当然要仔细分析数据，向老司机们学习，争取早日拿到驾照，安全驾驶。








 
## 文章数量 ##

![爬取文章数量](http://upload-images.jianshu.io/upload_images/1242974-7351f28b04771c0c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 发表文章作者 ##

懂球帝的快速发展是离不开内部员工以及球迷们的辛勤耕耘的，且看这些带领懂球帝一路扶摇直上的老司机们都是哪些人，有时间就关注他们领略他们的“风骚”~

![作者发文数量](http://upload-images.jianshu.io/upload_images/1242974-513b87888df87c91.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

创业不易，不光要写文章，之前在懂球帝直播里，看了你懂的老司机陈老板带领的懂球帝足球队与宝坁碧水源的足球比赛，文武兼备，深入群众啊 


![陈老板](http://upload-images.jianshu.io/upload_images/1242974-216ee897ee34d7f1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 内容来源 ##

 作为国内以内容运营为主的最大足球媒介，除了自身实力过硬之外，还博采众长，从其他站点引进优质的“外援”。

![懂球帝原创](http://upload-images.jianshu.io/upload_images/1242974-7f9acb7ec269f283.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

你可以感受下原创的文章数量与国外转载所占的比例，就知道为什么你懂在短短几年间吸引了这么多的用户。

![转载来源](http://upload-images.jianshu.io/upload_images/1242974-85a5eab012a77587.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

从以上图表可以看出，文章主要还是来自自身原创文章，所以这里主要选取了其他网站来源的文章，从上图可以看出我懂转载的文章主要来自于推特、新华社、阿斯报以及天空体育等，这在一定程度上是对这些站点文章质量的认可。

## 文章评论分析 ##
 
![文章全部评论](http://upload-images.jianshu.io/upload_images/1242974-f59f70f87bb6db18.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


我们不光要分析作者的发文数量，还要分析用户的关注度，寻找出最具价值的老司机，很显然，GreatWall、elfiemini、鹰旗百夫长以微弱优势占据三甲。恩恩，懂球帝最受欢迎老司机新鲜出炉啦。


|作者|全部评论数量|热评数量|
|:----:|:----:|:----:|
|GreatWall|238738|21873|
|elfiemini|224058|23014|
|鹰旗百夫长|200386|10337|

<br>

![各年度评论数据](http://upload-images.jianshu.io/upload_images/1242974-9953252a7b9ea2bb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
数据不能完全反应发展的实际情况，但不会撒谎，在一定程度上反应了懂球帝的快速发展。接下来，再单选2016年，评测各个月份的数据信息。

![2016年度评论数量](http://upload-images.jianshu.io/upload_images/1242974-685fc5e0b79cd0e3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 
乍看一眼，我惊呆了！为毛从6月开始，这评论数量就增长的这么高，匪夷所思。实际上，在7月初欧洲杯开始，球迷的关注度提高，各种话题不断展开以至于评论数量突飞猛进。
 
还有许多数据信息可挖掘，其他信息下次再撸，最后供上评论区的老司机们。
 
![评论获赞](http://upload-images.jianshu.io/upload_images/1242974-05507f06760b092b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 总结 #

本部分主要通过利用power-bi连接MySQL数据库，对数据进行可视化分析，主要涉及到的知识点包括以下部分：

- 统计分析
- 信息展示


# 参考资料 #

- scrapy-redis文档  
https://scrapy-redis.readthedocs.io/en/stable/readme.html
- redis-py文档 
http://redis-py.readthedocs.io/en/latest/
https://github.com/rolando/scrapy-redis
- [Python下用Scrapy和MongoDB构建爬虫系统
http://www.cnblogs.com/rrxc/p/4478936.html?utm_source=tuicool&utm_medium=referral
- 图片下载
http://doc.scrapy.org/en/latest/topics/item-pipeline.html
http://www.cnblogs.com/moon-future/p/5545828.html