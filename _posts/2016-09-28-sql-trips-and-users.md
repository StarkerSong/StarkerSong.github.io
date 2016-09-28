---
layout: post
title: "leetcode数据库总结（12）-Trips and Users"
date: 2016-09-28 21:23:47 +0800
categories: MySQL
tags: SQL
---
* content
{:toc}

![](http://i.imgur.com/I2Mk7KV.jpg)




















## 262. Trips and Users ##

### 表结构 ###


表：`Trips`

```sql
+----+-----------+-----------+---------+--------------------+----------+
| Id | Client_Id | Driver_Id | City_Id |        Status      |Request_at|
+----+-----------+-----------+---------+--------------------+----------+
| 1  |     1     |    10     |    1    |     completed      |2013-10-01|
| 2  |     2     |    11     |    1    | cancelled_by_driver|2013-10-01|
| 3  |     3     |    12     |    6    |     completed      |2013-10-01|
| 4  |     4     |    13     |    6    | cancelled_by_client|2013-10-01|
| 5  |     1     |    10     |    1    |     completed      |2013-10-02|
| 6  |     2     |    11     |    6    |     completed      |2013-10-02|
| 7  |     3     |    12     |    6    |     completed      |2013-10-02|
| 8  |     2     |    12     |    12   |     completed      |2013-10-03|
| 9  |     3     |    10     |    12   |     completed      |2013-10-03| 
| 10 |     4     |    13     |    12   | cancelled_by_driver|2013-10-03|
+----+-----------+-----------+---------+--------------------+----------+
```

表：`Users`

```sql
+----------+--------+--------+
| Users_Id | Banned |  Role  |
+----------+--------+--------+
|    1     |   No   | client |
|    2     |   Yes  | client |
|    3     |   No   | client |
|    4     |   No   | client |
|    10    |   No   | driver |
|    11    |   No   | driver |
|    12    |   No   | driver |
|    13    |   No   | driver |
+----------+--------+--------+
```


### 问题描述 ###

`Trips`表记录了所有的出租车出行记录。各条出行记录有唯一`Id`，并且在`Users`表中`Client_Id`和` Driver_Id`都是` Users_Id`的外键。`Status`是枚举类型（`completed`,`cancelled_by_driver`,`cancelled_by_client`）。

`Users`表记录了所有用户记录，每位用户有唯一的`User_Id`，`Role`是枚举类型（`client`, `driver`, `partner`）。

接下来需要查询未禁用用户在`Oct 1, 2013 `和`Oct 3, 2013`之间取消订单的比率（数据精度为两个小数点）。

> Write a SQL query to find the cancellation rate of requests made by **unbanned clients** between Oct 1, 2013 and Oct 3, 2013. For the above tables, your SQL query should return the following rows with the cancellation rate being rounded to two decimal places.

查询结果如下：

```sql
+------------+-------------------+
|     Day    | Cancellation Rate |
+------------+-------------------+
| 2013-10-01 |       0.33        |
| 2013-10-02 |       0.00        |
| 2013-10-03 |       0.50        |
+------------+-------------------+
```

### 解决方案 ###

在上面的题目描述中需要注意引用加粗的部分，题目描述有问题，在查询的时候我一直使用`Status='cancelled_by_client'`导致测试一直无法通过。后来在讨论区中发现有同学的回复。

> sorry, I described the question incorrectly. It should have been "Write a SQL query to find the cancellation rate of requests made by unbanned users".  by cak1erlizhou

后来直接将查询条件`t.Status='cancelled_by_client'`改成`t.Status<>'completed'`测试用例就通过啦。

查询时可分步骤为：

1. 将`Trips`表和`Users`表关联起来
2. 筛选指定条件的数据，即`u.Banned='No' and (t.Request_at between '2013-10-01' and '2013-10-03')`
3. 根据时间分组
4. 在条件中使用聚集函数统计取消的订单数量以及订单总数
5. 计算取消率以及精度值

```sql
select t.Request_at Day,
round(sum(case when  t.Status<>'completed' then 1 else 0 end) /count(1),2) 'Cancellation Rate'
from Trips t,Users u
where u.Users_Id=t.Client_Id and u.Banned='No' and (t.Request_at between '2013-10-01' and '2013-10-03')
group by t.Request_at

```


### 总结 ###

本篇设计的知识点主要包括：

- round精度的使用
- case when判断条件的使用
- group by分组
- between 范围查询