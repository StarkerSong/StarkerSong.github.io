---
layout: post
title: "leetcode数据库总结（11）-Rising Temperature"
date: 2016-09-28 20:00:53 +0800
categories: MySQL
tags: SQL
---
* content
{:toc}

![](http://i.imgur.com/I2Mk7KV.jpg)

















## 197. Rising Temperature ##

### 表结构 ###

表：`Weather`

```sql
+---------+------------+------------------+
| Id(INT) | Date(DATE) | Temperature(INT) |
+---------+------------+------------------+
|       1 | 2015-01-01 |               10 |
|       2 | 2015-01-02 |               25 |
|       3 | 2015-01-03 |               20 |
|       4 | 2015-01-04 |               30 |
+---------+------------+------------------+
```

### 问题描述 ###


给定上面的表，找出与之前天气温度较高的`Id`。例如返回下面的表：

```sql
+----+
| Id |
+----+
|  2 |
|  4 |
+----+
```

### 解决方案 ###

本篇归根到底是考察`MySQL`中的时间函数，通过连接表，对比相邻两天的温度差值。

```sql
select w1.Id 
from Weather w1,Weather w2
where  w1.Temperature>w2.Temperature 
and DATEDIFF(w1.Date,w2.Date)=1
```

### 总结 ###

本篇主要考察`MySQL`的时间日期函数


- NOW()	返回当前的日期和时间
- CURDATE()	返回当前的日期
- CURTIME()	返回当前的时间
- DATE()	提取日期或日期/时间表达式的日期部分
- EXTRACT()	返回日期/时间按的单独部分
- DATE_ADD()	给日期添加指定的时间间隔
- DATE_SUB()	从日期减去指定的时间间隔
- DATEDIFF()	返回两个日期之间的天数
- DATE_FORMAT()	用不同的格式显示日期/时间

可参考文档：
[http://www.w3school.com.cn/sql/sql_dates.asp](http://www.w3school.com.cn/sql/sql_dates.asp)


