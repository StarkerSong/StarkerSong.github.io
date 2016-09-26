---
layout: post
title:  "leetcode数据库总结（8）-Customers Who Never Order"
date: 2016-09-26 22:05:53 +0800
categories: MySQL
tags: SQL
---
* content
{:toc}

![](http://i.imgur.com/I2Mk7KV.jpg)












## 183. Customers Who Never Order ##

### 表结构 ###

表：`table`

```sql
+----+-------+
| Id | Name  |
+----+-------+
| 1  | Joe   |
| 2  | Henry |
| 3  | Sam   |
| 4  | Max   |
+----+-------+
```

表：`Orders`

```sql
+----+------------+
| Id | CustomerId |
+----+------------+
| 1  | 3          |
| 2  | 1          |
+----+------------+
```

### 问题描述 ###

根据上述的两张表查找出从未下单的用户。例如上述表中返回的结果为：

```sql
+-----------+
| Customers |
+-----------+
| Henry     |
| Max       |
+-----------+
```

### 解决方案 ###

**子查询方案：**

这题首先想到的是子查询操作，查找不在列表中的数据。

```sql
select Customers.Name Customers 
from Customers  
where Customers.Id 
not in
(select CustomerId from Orders)
```

但是子查询比较耗费时间，这个语句执行时间为：522 ms

**左连接操作:**

通常可以将子查询转为非子查询操作，在下面的语句中通过`left`操作获得查询结果。

```sql
select c.Name Customers 
from Customers c
left join Orders o
on  c.Id=o.CustomerId  
where o.CustomerId is null

```

这个语句执行时间为：475 ms

### 总结 ###

考察子查询操作的知识，提升查询性能。

