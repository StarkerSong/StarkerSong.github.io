---
layout: post
title: "MySQL参数性能优化"
date: 2016-09-10 21:37:52 +0800
categories: MySQL
tags: MySQL Performance
---
* content
{:toc}

![](http://i.imgur.com/rVrPrRJ.jpg)








在网易面试，曾经问过我有关`MySQL`参数性能优化的内容，虽然各个参数都有接触并且混个脸熟，但是当时非常紧张处于懵逼状态，只想起了`innodb_buffer_pool_size`这个参数，毕竟要对得起我之前研究过数据库的LRU算法啊。在没有结合具体的业务前提下，现在只能拿来主义再闭门造车了，如果能有高手指点真的是荣幸之至啊

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

### max_connections=100 ###

mysql服务器支持的最大并发连接数（用户数）。但总会预留其中的一个连接给管理员使用超级权限登录，即使连接数目达到最大限制。如果设置得过小而用户比较多，会经常出现“Too many connections”错误。

### query_cache_size=0 ###

查询缓存大小，用于缓存`SELECT`查询结果。如果有许多返回相同查询结果的`SELECT`查询，并且很少改变表，可以设置`query_cache_size`大于0，可以极大改善查询效率。而如果表数据频繁变化，就不要使用这个，会适得其反

### table_cache=256 ###

这个参数在5.1.3之后的版本中叫做`table_open_cache`，用于设置table高速缓存的数量。由于每个客户端连接都会至少访问一个表，因此此参数的值与`max_connections`有关。当某一连接访问一个表时，MySQL会检查当前已缓存表的数量。如果该表已经在缓存中打开，则会直接访问缓存中的表已加快查询速度；如果该表未被缓存，则会将当前的表添加进缓存并进行查询。在执行缓存操作之前，`table_cache`用于限制缓存表的最大数目：如果当前已经缓存的表未达到`table_cache`，则会将新表添加进来；若已经达到此值，MySQL将根据缓存表的最后查询时间、查询率等规则释放之前的缓存。

### tmp_table_size=34M ###

内存中的每个临时表允许的最大大小。如果临时表大小超过该值，临时表将自动转为基于磁盘的表（Disk Based Table）。

### thread_cache_size=8 ###

缓存的最大线程数。当客户端连接断开时，如果客户端总连接数小于该值，则处理客户端任务的线程放回缓存。在高并发情况下，如果该值设置得太小，就会有很多线程频繁创建，线程创建的开销会变大，查询效率也会下降。一般来说如果在应用端有良好的多线程处理，这个参数对性能不会有太大的提高。


## InnoDB相关参数 ##

### innodb_additional_mem_pool_size=3M ### 

InnoDB用于存储元数据信息的内存池大小，一般不需修改

### innodb_flush_log_at_trx_commit =1 ### 

事务相关参数，如果值为`1`，则`InnoDB`在每次`commit`都会将事务日志写入磁盘（磁盘IO消耗较大），这样保证了完全的`ACID`特性。而如果设置为0，则表示事务日志写入内存log和内存log写入磁盘的频率都为1次/秒。如果设为2则表示事务日志在每次`commit`都写入内存log，但内存log写入磁盘的频率为1次/秒。

### innodb_log_buffer_size=2M ### 

InnoDB日志数据缓冲大小，如果缓冲满了，就会将缓冲中的日志数据写入磁盘（flush）。由于一般至少都1秒钟会写一次磁盘，所以没必要设置过大，即使是长事务。

### innodb_buffer_pool_size=105M ### 

InnoDB使用缓冲池来缓存索引和行数据。该值设置的越大，则磁盘IO越少。一般将该值设为物理内存的80%。

### innodb_log_file_size=53M ### 

每一个InnoDB事务日志的大小。一般设为`innodb_buffer_pool_size`的25%到100%


### innodb_thread_concurrency=9 ### 

InnoDB内核最大并发线程数


## 参考资料 ##


[[笔记]MySQL 配置优化](http://www.cnblogs.com/feichexia/archive/2012/11/27/mysqlconf.html) http://www.cnblogs.com/feichexia/archive/2012/11/27/mysqlconf.html 