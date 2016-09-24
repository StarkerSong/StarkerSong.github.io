---
layout: post
title: "leetcode数据库总结（5）-Consecutive Numbers"
date: 2016-09-24 21:55:38 +0800
categories: MySQL
tags: SQL
---
* content
{:toc}

![](http://i.imgur.com/I2Mk7KV.jpg)










## 180. Consecutive Numbers ##

### 表结构 ###


```sql
+----+-----+
| Id | Num |
+----+-----+
| 1  |  1  |
| 2  |  1  |
| 3  |  1  |
| 4  |  2  |
| 5  |  1  |
| 6  |  2  |
| 7  |  2  |
+----+-----+
```

### 问题描述 ###

写一个`SQL`语句，给定以上`Logs`表，查询连续出现至少三次的数据。例如在上面的表中，`1`就连续出现有3次。


### 解决方案 ###

在上面的题目中要求连续出现相同数据，那么只能根据`Id`来进行绑定，由于需要寻找3个连续出现的数据，所以需要用到3个表，然后等值关联，需要注意的是查找出来的数据是有重复的，需要加上`distinct`去掉重复的数据。

```sql
select distinct l1.Num ConsecutiveNums
from Logs l1,Logs l2,Logs l3
where l1.Id+1=l2.Id 
and l2.Id+1=l3.Id 
and l1.Num=l2.Num 
and l1.Num=l3.Num
```


### 总结 ###

这篇讲解的和上篇比较相同，该查询重点在于建立业务的分析模型，进行表连接操作，提取不重复数据。


