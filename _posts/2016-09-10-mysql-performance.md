---
layout: post
title: "MySQL参数性能优化"
date: 2016-09-10 21:37:52 +0800
categories: MySQL
tags: MySQL Performance
---
* content
{:toc}

![](http://i.imgur.com/q5x4JOK.png)







在网易面试，曾经问过我有关`MySQL`参数性能优化的内容，虽然各个参数都有接触并且混个脸熟，但是当时非常紧张处于懵逼状态，只想起了`innodb_buffer_pool_size`这个参数，毕竟要对得起我之前研究过数据库的LRU算法啊。在没有结合具体的业务前提下，现在只能拿来主义再闭门造车了，如果能有高手指点真的是荣幸之至啊

在此之前先查看先数据库版本

```sql
mysql> select version();
+-----------+
| version() |
+-----------+
| 5.7.13    |
+-----------+
1 row in set (0.26 sec)

```

## 参数设置 ##

在安装完`MySQL`后，查看`my-default.ini`文件里面的参数设置，在`MySQL`中要查看配置参数可以使用下面的命令：

```sql
show variables like '%innodb%'; #查看innodb相关配置参数
show status like '%innodb%'; #查看innodb相关的运行时参数
show global status like 'open%tables';#查看全局的运行参数，加上global是对当前mysql服务器中运行的所有数据库实例进行统计。不加global则只对当前数据库实例进行统计。
```

```sql
[mysqld]
port=3306 # mysql服务端默认监听(listen on)的TCP/IP端口

basedir="D:\MySQL" # 基准路径，其他路径都相对于这个路径

datadir="D:\MySQL\data" # mysql数据库文件所在目录

character-set-server=latin1 # 服务端使用的字符集默认为8比特编码的latin1字符集

default-storage-engine=INNODB # 创建新表时将使用的默认存储引擎

sql-mode="STRICT_TRANS_TABLES,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION" # SQL模式为strict模式

```

### max_connections ###

```sql

mysql> show variables like '%max_connections%';
+-----------------+-------+
| Variable_name   | Value |
+-----------------+-------+
| max_connections | 200   |
+-----------------+-------+
1 row in set, 1 warning (0.00 sec)
```

mysql服务器支持的最大并发连接数（用户数）。但总会预留其中的一个连接给管理员使用超级权限登录，即使连接数目达到最大限制。如果设置得过小而用户比较多，会经常出现“Too many connections”错误。

### query_cache_size ###

```sql
mysql> show variables like '%query_cache_size%';
+------------------+---------+
| Variable_name    | Value   |
+------------------+---------+
| query_cache_size | 1048576 |
+------------------+---------+
1 row in set, 1 warning (0.00 sec)
```
查询缓存大小，用于缓存`SELECT`查询结果。如果有许多返回相同查询结果的`SELECT`查询，并且很少改变表，可以设置`query_cache_size`大于0，可以极大改善查询效率。

### table_open_cache ###

```sql

mysql> show variables like '%table_open_cache%';
+----------------------------+-------+
| Variable_name              | Value |
+----------------------------+-------+
| table_open_cache           | 2000  |
| table_open_cache_instances | 16    |
+----------------------------+-------+
2 rows in set, 1 warning (0.00 sec)
```

这个参数用于设置table高速缓存的数量。由于每个客户端连接都会至少访问一个表，因此此参数的值与`max_connections`有关。当某一连接访问一个表时，MySQL会检查当前已缓存表的数量。如果该表已经在缓存中打开，则会直接访问缓存中的表已加快查询速度；如果该表未被缓存，则会将当前的表添加进缓存并进行查询。在执行缓存操作之前，`table_cache`用于限制缓存表的最大数目：如果当前已经缓存的表未达到`table_cache`，则会将新表添加进来；若已经达到此值，MySQL将根据缓存表的最后查询时间、查询率等规则释放之前的缓存。

### tmp_table_size ###

```sql
mysql> show variables like '%tmp_table_size%';
+----------------+----------+
| Variable_name  | Value    |
+----------------+----------+
| tmp_table_size | 16777216 |
+----------------+----------+
1 row in set, 1 warning (0.00 sec)

```

内存中的每个临时表允许的最大大小。如果临时表大小超过该值，临时表将自动转为基于磁盘的表（Disk Based Table）。

### thread_cache_size ###

```sql
mysql> show variables like '%thread_cache_size%';
+-------------------+-------+
| Variable_name     | Value |
+-------------------+-------+
| thread_cache_size | 10    |
+-------------------+-------+
1 row in set, 1 warning (0.00 sec)
```

缓存的最大线程数。当客户端连接断开时，如果客户端总连接数小于该值，则处理客户端任务的线程放回缓存。在高并发情况下，如果该值设置得太小，就会有很多线程频繁创建，线程创建的开销会变大，查询效率也会下降。一般来说如果在应用端有良好的多线程处理，这个参数对性能不会有太大的提高。


## InnoDB相关参数 ##


### innodb_buffer_pool_size ### 

```sql

mysql> show variables like '%innodb_buffer_pool_size%';
+-------------------------+-----------+
| Variable_name           | Value     |
+-------------------------+-----------+
| innodb_buffer_pool_size | 134217728 |
+-------------------------+-----------+
1 row in set, 1 warning (0.00 sec)
```

InnoDB使用缓冲池来缓存索引和行数据。该值设置的越大，则磁盘IO越少。一般将该值设为物理内存的80%。

### innodb_log_file_size ### 

```sql
mysql> show variables like '%innodb_log_file_size%';
+----------------------+----------+
| Variable_name        | Value    |
+----------------------+----------+
| innodb_log_file_size | 50331648 |
+----------------------+----------+
1 row in set, 1 warning (0.00 sec)

```

这是`redo`日志的大小。`redo`日志被用于确保写操作快速而可靠并且在崩溃时恢复。一直到`MySQL 5.1`，它都难于调整，因为一方面你想让它更大来提高性能，另一方面你想让它更小来使得崩溃后更快恢复。幸运的是从`MySQL 5.5`之后，崩溃恢复的性能的到了很大提升，这样你就可以同时拥有较高的写入性能和崩溃恢复性能了。一直到`MySQL 5.5`，`redo`日志的总尺寸被限定在4GB(默认可以有2个log文件)。在`MySQL 5.6`里被提高。一开始就把`innodb_log_file_size`设置成512M(这样有1GB的redo日志)会使你有充裕的写操作空间。如果你知道你的应用程序需要频繁的写入数据并且你使用的时`MySQL 5.6`，你可以一开始就把它这是成4G。


## 总结 ##

以上部分主要关于MySQL参数性能优化，大体可以分为两类即连接优化和缓存优化。当然上述只是列举了部分常用的参数，并且对于数据库的优化还可以从数据库的

- 索引
- SQL语句
- 数据类型
- 表结构
- 系统环境
- 硬件配置

等多个方面进行优化。

## 参考资料 ##


[[笔记]MySQL 配置优化](http://www.cnblogs.com/feichexia/archive/2012/11/27/mysqlconf.html) http://www.cnblogs.com/feichexia/archive/2012/11/27/mysqlconf.html 

[解开发者之痛：中国移动MySQL数据库优化最佳实践](https://www.sdk.cn/news/4746)https://www.sdk.cn/news/4746