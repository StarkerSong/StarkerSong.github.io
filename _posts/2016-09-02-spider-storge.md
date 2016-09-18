---
layout: post
title: "基于scrapy-redis分布式网络爬虫存储数据分析（二）"
date: 2016-09-02 15:38:55 +0800
categories: scrapy-redis
tags: spider MySQL redis mongodb
---
* content
{:toc}
 

本系列文章主要在简书上发布，图片链接如果有问题可直接访问我的简书页面：
[基于scrapy-redis分布式网络爬虫存储数据分析](http://www.jianshu.com/p/8f1e60ce8134)


# 文件目录结构 #

在Windows的命令窗口中输入`tree /f  dqd`命令，出现以下文件目录结构： 

``` python
C:\Python27\Scripts>tree /f  dqd
文件夹 PATH 列表
卷序列号为 A057-81B6
C:\PYTHON27\SCRIPTS\DQD
│  docker-compose.yml
│  Dockerfile
│  mongodb2mysql.py
│  process_items.py
│  scrapy.cfg
│
├─.idea
│      dqd.iml
│      misc.xml
│      modules.xml
│      workspace.xml
│
├─dqd
│  │  image_pipelines.py
│  │  image_pipelines.pyc
│  │  items.py
│  │  mongo_pipelines.py
│  │  mongo_pipelines.pyc
│  │  mysql_pipelines.py
│  │  mysql_pipelines.pyc
│  │  redis_pipelines.py
│  │  redis_pipelines.pyc
│  │  settings.py
│  │  settings.pyc
│  │  __init__.py
│  │  __init__.pyc
│  │
│  └─spiders
│          dqdspider.py
│          dqdspider.pyc
│          __init__.py
│          __init__.pyc
│
└─Image
    └─full
        │  full.rar
        │
        └─女球迷采访：由萌yolanda
                480-150605104925433.jpg
                480-150605104940P1.jpg
                480-15060510522UT.jpg
                480-150605105242F9.jpg
                480-15060510525X18.jpg
                480-150605105312V0.jpg

```








## 下载和存储管理 ##

### settings.py设置 ### 

```python
BOT_NAME = 'dqd'

SPIDER_MODULES = ['dqd.spiders']
NEWSPIDER_MODULE = 'dqd.spiders'

USER_AGENT = 'Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1; 360SE)'
DUPEFILTER_CLASS = "scrapy_redis.dupefilter.RFPDupeFilter"
SCHEDULER = "scrapy_redis.scheduler.Scheduler"
SCHEDULER_PERSIST = True
SCHEDULER_QUEUE_CLASS = "scrapy_redis.queue.SpiderPriorityQueue"
#SCHEDULER_QUEUE_CLASS = "scrapy_redis.queue.SpiderQueue"
#SCHEDULER_QUEUE_CLASS = "scrapy_redis.queue.SpiderStack"

ITEM_PIPELINES = {
    # 'dqd.image_pipelines.DownloadImagesPipeline':1,   #下载图片
    'dqd.redis_pipelines.DqdPipeline': 200,
    'scrapy_redis.pipelines.RedisPipeline': 300,
    'dqd.mongo_pipelines.MongoDBPipeline':400,
    'dqd.mysql_pipelines.MySQLPipeline': 1
}
IMAGES_STORE='.\Image'

# redis 在process_items.py文件中进行设置

#################    MONGODB     #############################
MONGODB_SERVER='localhost'
MONGODB_PORT=27017
MONGODB_DB='dqd_db'
MONGODB_COLLECTION='dqd_collection'

####################    MYSQL      #############################
MYSQL_HOST = 'localhost'
MYSQL_DBNAME = 'dqd_database'
MYSQL_USER = 'root'
MYSQL_PASSWD = '******'

LOG_LEVEL = 'DEBUG'
DEPTH_LIMIT=1
# Introduce an artifical delay to make use of parallelism. to speed up the
# crawl.
DOWNLOAD_DELAY = 0.2

```

当Item在Spider中被收集之后，它将会被传递到Item Pipeline，一些组件会按照一定的顺序执行对Item的处理。

每个item pipeline组件(有时称之为“Item Pipeline”)是实现了简单方法的Python类。他们接收到Item并通过它执行一些行为，同时也决定此Item是否继续通过pipeline，或是被丢弃而不再进行处理。

- 清理HTML数据
- 验证爬取的数据(检查item包含某些字段)
- 查重(并丢弃)
- 将爬取结果保存到数据库中

### image_pipelines.py ###

```python
# -*- coding: utf-8 -*-
import scrapy
from scrapy.pipelines.images import ImagesPipeline
from scrapy.exceptions import DropItem
from scrapy import Request
import codecs

class DownloadImagesPipeline(ImagesPipeline):
    def get_media_requests(self,item,info): #下载图片
        for image_url in item['image_urls']:
            yield Request(image_url,meta={'item':item,'index':item['image_urls'].index(image_url)}) #添加meta是为了下面重命名文件名使用

    def file_path(self,request,response=None,info=None):
        item=request.meta['item'] #通过上面的meta传递过来item
        index=request.meta['index'] #通过上面的index传递过来列表中当前下载图片的下标

        #图片文件名 
        image_guid = request.url.split('/')[-1]
        #图片下载目录  
        filename = u'full/{0}/{1}'.format(item['news_title'], image_guid)
        return filename

```
 以下图片为下载内容

![图片下载](http://upload-images.jianshu.io/upload_images/1242974-6fd8cbe3a8880971.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 
### redis_pipelines.py ### 

```python
# -*- coding: utf-8 -*-

# Define your item pipelines here
#
# Don't forget to add your pipeline to the ITEM_PIPELINES setting
# See: http://doc.scrapy.org/en/latest/topics/item-pipeline.html
from datetime import datetime

class DqdPipeline(object):
    def process_item(self, item, spider):
        item["crawled"] = datetime.utcnow()
        item["spider"] = spider.name
        return item

```
 在截图中，dqdspider中应该有3个队列，但是因为我已经下载完毕，所以`dqdspider:request`队列自动删除了。

- `dqdspider:request`待爬队列
- `dqdspider:dupefilter`用来过滤重复的请求
- `dqdspider:items`爬取的信息内容

![redis](http://upload-images.jianshu.io/upload_images/1242974-1b355ef6716ecce8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### mongo_pipelines.py ### 

```python
# -*- coding:utf-8 -*-
import pymongo
from scrapy.exceptions import DropItem
from scrapy.conf import settings
# from scrapy import log


class MongoDBPipeline(object):
    #Connect to the MongoDB database
    def __init__(self):
        connection = pymongo.MongoClient(settings['MONGODB_SERVER'], settings['MONGODB_PORT'])
        db = connection[settings['MONGODB_DB']]
        self.collection = db[settings['MONGODB_COLLECTION']]



    def process_item(self, item, spider):
        valid=True
        for data in item:
            if not data:
                valid=False
                raise DropItem('Missing{0}!'.format(data))
        if valid:

            self.collection.insert(dict(item))
            log.msg('question added to mongodb database!',
                    level=log.DEBUG,spider=spider)
        return item
```

为了展示MongoDB中的数据内容使用了管理工具Robomongo查看爬取的内容。


![Robomongo.png](http://upload-images.jianshu.io/upload_images/1242974-8031753fc6bc3477.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### mysql_pipelines.py ### 

```python
# -*- coding:utf-8 -*-
from scrapy.conf import settings
import MySQLdb

_DEBUG=True

class MySQLPipeline(object):
    #Connect to the MySQL database
    def __init__(self):
        self.conn =  MySQLdb.connect(
            user=settings['MYSQL_USER'],
            passwd=settings['MYSQL_PASSWD'],
            db=settings['MYSQL_DBNAME'],
            host=settings['MONGODB_SERVER'],
            charset='utf8',
            use_unicode = True
        )
        self.cursor=self.conn.cursor()
        #清空表：注意区分和delete的区别
        self.cursor.execute("truncate table news_main;") #清空表的信息
        self.cursor.execute("truncate table news_comment;") #清空表的信息
        self.conn.commit()

    def process_item(self, item, spider):
        try:
            self.insert_news(item)     #将文章信息插入到数据库中
            self.insert_comment(item,item["source_url"])     # 将评论信息信息插入到数据库中
            self.conn.commit()

        except MySQLdb.Error as e:
                print (("Error %d: %s") % (e.args[0],e.args[1]))
        return item

    #将文章信息插入到数据库中
    def insert_news(self,item):
        args = (item["source_url"], item["news_title"], item["news_author"],
                item["news_time"], item["news_content"],item["news_source"],
                item["news_allCommentAllCount"],  item["news_hotCommentHotCount"])

        newsSqlText = "insert into news_main(" \
                      "news_url,news_title,news_author,news_time,news_content,news_source," \
                      "news_commentAllCount,news_commentHotCount) " \
                      "values ('%s','%s','%s','%s','%s','%s','%s','%s')" % args
        self.cursor.execute(newsSqlText)
        self.conn.commit()

    # 将评论信息信息插入到数据库中
    def insert_comment(self, item,url):
        #因为评论是列表，以下为并列迭代
        for comment_content,comment_author,comment_time,comment_likeCount \
                in zip(item["news_hotCommentContent"],item["news_hotCommentAuthor"],
                     item["news_hotCommentTime"],item["news_hotCommentLikeCount"]):
            newsSqlText = "insert into news_comment(comment_content,comment_author,comment_time,comment_likeCount,source_url) " \
                          "values (\"%s\",'%s','%s','%s','%s')" % (comment_content,comment_author,comment_time,comment_likeCount[2:-1],url)
            # #加入调试代码 监视newsSqlText的取值
            # if _DEBUG == True:
            #     import pdb
            #     pdb.set_trace()
            # self.cursor.execute(newsSqlText.encode().decode("unicode-escape").replace('\\','').replace('\']','').replace('[u\'','').strip())
            self.cursor.execute(newsSqlText.encode().decode("unicode-escape").replace('\\',''))
            self.conn.commit()

```

在这里将爬取的信息进行清洗和转储。
 
![MySQL](http://upload-images.jianshu.io/upload_images/1242974-9375652d82588dd7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 总结 #

本部分主要介绍了数据的存储，主要为图片以及文章内容部分的存储，涉及到的知识点包括以下部分：

- mysql建表、数据查询、数据操纵、索引优化
- redis存储队列
- mongodb存储
- tree命令