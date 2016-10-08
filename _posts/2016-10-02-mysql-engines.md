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

在MySQL5.1版本之前，MyISAM是默认的存储引擎，它提供了大量的特性包括全文索引、压缩、空间函数(GIS)等,但不支持事务和行级锁，且崩溃后无法完全恢复。如果表主要是用于插入新记录和读出记录，那么选择MyISAM存储引擎能实现处理的高效率。


### Archive ###

Archive只支持`Insert`和`Select`操作，每次`select`查询都需要执行全表扫描，Archive表适合日志和数据采集类应用。Archive支持行级锁和专用缓冲区，可实现高并发的插入。

### Blackhole ###

Blackhole引擎没有实现任何的存储机制，它会丢弃所有插入的数据，不做任何保存，但服务器会记录Blackhole表的日志，所以可以用于复制数据到备库。可在特殊的复制架构和日志审核时发挥作用。


### CSV ###

不支持索引，可以将普通的CSV文件作为MySQL的表来处理。可以在数据库运行时拷入或者拷出文件。CSV引擎可以作为数据交换的机制。

### Federated ###

Federated引擎是访问其他MySQL服务器的代理，会创建到远程MySQL服务器的客户端连接，并将查询传输到远程服务器执行，然后提取或者发送需要的数据。

### Memory ###

所有保存数据存储在内存中，不需要进行磁盘I/O,Memory表的结构在重启后还会保留，但数据会消失。Memory表支持hash索引，查找速度快。表级锁，并发写入性能较低。它不支持blob或text类型的列，并且每行的长度是固定的。

主要应用：

- 查找或者映射表
- 缓存周期性聚合数据的结果
- 保存数据分析中产生的中间数据


### Mgr_MyISAM ###

Mgr_MyISAM是MyISAM引擎的一个变种。Merge表由多个表结构相同的MyISAM表合并而成的虚拟表。可用于日志或者数据仓库类应用。下面简要介绍如何建立Merge表。

**创建tb_log1**

```sql
mysql> create table tb_log1(
    -> id int not null auto_increment,
    -> log varchar(50),
    -> primary key(id)
    -> )engine=myisam;
Query OK, 0 rows affected (0.43 sec)
```

表结构为：

```sql
mysql> desc tb_log1;
+-------+-------------+------+-----+---------+----------------+
| Field | Type        | Null | Key | Default | Extra          |
+-------+-------------+------+-----+---------+----------------+
| id    | int(11)     | NO   | PRI | NULL    | auto_increment |
| log   | varchar(50) | YES  |     | NULL    |                |
+-------+-------------+------+-----+---------+----------------+
2 rows in set (0.27 sec)
```


**tb_log1表插入数据**

```sql
mysql> insert into tb_log1(log) values ('tb_log1_1');
Query OK, 1 row affected (0.24 sec)

mysql> insert into tb_log1(log) values ('tb_log1_2');
Query OK, 1 row affected (0.01 sec)

mysql> insert into tb_log1(log) values ('tb_log1_3');
Query OK, 1 row affected (0.00 sec)
```


**创建tb_log2**

```sql
mysql> create table tb_log2(
    -> id int not null auto_increment,
    -> log varchar(50),
    -> primary key(id)
    -> )engine=myisam;
Query OK, 0 rows affected (0.43 sec)
```
表结构为：

```sql
mysql> desc tb_log2;
+-------+-------------+------+-----+---------+----------------+
| Field | Type        | Null | Key | Default | Extra          |
+-------+-------------+------+-----+---------+----------------+
| id    | int(11)     | NO   | PRI | NULL    | auto_increment |
| log   | varchar(50) | YES  |     | NULL    |                |
+-------+-------------+------+-----+---------+----------------+
2 rows in set (0.00 sec)
```

**tb_log2表插入数据**

```sql
mysql> insert into tb_log2(log) values ('tb_log2_1');
Query OK, 1 row affected (0.24 sec)

mysql> insert into tb_log2(log) values ('tb_log2_2');
Query OK, 1 row affected (0.01 sec)

mysql> insert into tb_log2(log) values ('tb_log2_3');
Query OK, 1 row affected (0.00 sec)
```


**创建tb_merge**

```sql
mysql> create table tb_merge(
    -> id int not null auto_increment,
    -> log varchar(50),
    -> primary key(id)
    -> )engine=merge
    ->  union(tb_log1,tb_log2) insert_method=last;
Query OK, 0 rows affected (0.09 sec)
```

表结构为：

```sql

mysql> desc tb_merge;
+-------+-------------+------+-----+---------+----------------+
| Field | Type        | Null | Key | Default | Extra          |
+-------+-------------+------+-----+---------+----------------+
| id    | int(11)     | NO   | PRI | NULL    | auto_increment |
| log   | varchar(50) | YES  |     | NULL    |                |
+-------+-------------+------+-----+---------+----------------+
2 rows in set (0.01 sec)
```

**查询tb_merge表数据**

```sql
mysql> select * from tb_merge;
+----+-----------+
| id | log       |
+----+-----------+
|  1 | tb_log1_1 |
|  2 | tb_log1_2 |
|  3 | tb_log1_3 |
|  1 | tb_log2_3 |
|  2 | tb_log2_2 |
|  3 | tb_log2_1 |
+----+-----------+
6 rows in set (0.01 sec)
```

1. ENGINE=MERGE
	指明使用MERGE引擎，或者指定ENGINE=MRG_MyISAM也可以。
2. UNION=(t1, t2)
	指明了MERGE表中连接的表，可以通过alter table的方式修改UNION的值，以实现增删MERGE表子表的功能。比如：
 	```sql
	alter table tb_merge engine=merge union(tb_log1) insert_method=last;
	```
3. INSERT_METHOD=LAST
	INSERT_METHOD指明插入方式，取值可以是：0 不允许插入；FIRST 插入到UNION中的第一个表； LAST 插入到UNION中的最后一个表。
4. MERGE表及构成MERGE数据表结构的各成员数据表必须具有完全一样的结构。每一个成员数据表的数据列必须按照同样的顺序定义同样的名字和类型，索引也必须按照同样的顺序和同样的方式定义。

### 如何选择存储引擎 ###

选择标准可以分为：

- 是否需要支持事务
- 是否需要使用热备
- 崩溃恢复：能否接受崩溃
- 是否需要外键支持


然后按照标准，选择对应的存储引擎即可。

### 转换表的引擎  ###

1. Alter table



	```sql
	alter table mytable engine=InnoDB;
	```

	上述方法适用于任何存储引擎，但需要执行很长时间。MySQL会按行将数据从原表复制到新表中，复制期间可能会消耗所有的I/O能力,同时会在原表上加上读锁。转换表的存储引擎会失去和原引擎相关的所有特性。

2. 导出与导入
	为更好控制转换过程，使用mysqldump工具将数据导出到文件，修改文件中create table语句的存储引擎选项，同时修改表名（相同数据库不能使用相同表名，即便引擎不同）。mysqldump默认会自动在create table语句前加上drop table语句。

3. 创建与查询

	综合第一种方法的高效和第二种方法的安全。不需要导出整个表的数据，而是先创建一个新的存储引擎的表，然后利用`insert...select`语法来导数据。（《高性能MySQL》p28）









## 参考资料 ##

- [16.1 Setting the Storage Engine](http://dev.mysql.com/doc/refman/5.7/en/storage-engine-setting.html)http://dev.mysql.com/doc/refman/5.7/en/storage-engine-setting.html

- [MySQL存储引擎介绍](http://www.jellythink.com/archives/640)http://www.jellythink.com/archives/640
