---
layout: post
title: "MySQL数据库(2)-存储引擎"
date: 2016-10-02 19:15:18 +0800
categories: MySQL
tags: lock transaction
---
* content
{:toc}

![](http://i.imgur.com/TxPjy6g.jpg)


在命令窗口中可以通过`show engines`命令查询MySQL支持的存储引擎。各个存储引擎有各自的优势，在选用时应该根据具体的业务选择合适的存储引擎。

![](http://i.imgur.com/DYWs0wP.png)
 

## 各类存储引擎 ##

### InnoDB ###

InnoDB是通用的高可用、高性能的存储引擎，在MySQL中，InnoDB是默认的存储引擎。它的优势具体来说有以下方面：

- DML操作遵循ACID，通过事务的提交、回滚、故障修复保护用户数据。
- 行级锁与Oracle类型的一致性读增强多用户的并发性。
- InnoDB表基于聚簇索引建立的，通过主键优化查询，减少I/O次数。
- 为保持数据的一致性，InnoDB支持外键约束。
- InnoDB通过一些机制和工具支持真正的热备份。


### MyISAM ###

在MySQL5.1版本之前，MyISAM是默认的存储引擎，它提供了大量的特性包括全文索引、压缩、空间函数(GIS)等,但不支持事务和行级锁，且崩溃后无法完全恢复。



## 参考资料 ##

[16.1 Setting the Storage Engine](http://dev.mysql.com/doc/refman/5.7/en/storage-engine-setting.html)http://dev.mysql.com/doc/refman/5.7/en/storage-engine-setting.html


（未完）
