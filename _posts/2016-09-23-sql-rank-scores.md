---
layout: post
title: "leetcode数据库总结（4）-Rank Scores"
date: 2016-09-23 21:33:40 +0800
categories: MySQL
tags: SQL
---
* content
{:toc}

![](http://i.imgur.com/I2Mk7KV.jpg)












## 178. Rank Scores ##

### 表结构 ###


```sql
+----+-------+
| Id | Score |
+----+-------+
| 1  | 3.50  |
| 2  | 3.65  |
| 3  | 4.00  |
| 4  | 3.85  |
| 5  | 4.00  |
| 6  | 3.65  |
+----+-------+
```

### 问题描述 ###


写个`SQL`语句查询分数排行。如果分数相同，那么排名相同。下个排名应该是连续的整数，即排名间没有间隔。最后排名的结果如下表：

```sql
+-------+------+
| Score | Rank |
+-------+------+
| 4.00  | 1    |
| 4.00  | 1    |
| 3.85  | 2    |
| 3.65  | 3    |
| 3.65  | 3    |
| 3.50  | 4    |
+-------+------+
```

### 解决方案 ###



```sql
# Write your MySQL query statement below

select Score,
(select count(1) from 
        (select distinct Score from Scores) s 
        where s.Score>=Scores.Score
        )   Rank 
from Scores  
order by Score desc
```


可以将上面的语句拆分，先使用`select distinct Score from Scores`查询去除重复的数据元素。然后再从`Scores`表中查询分数，统计`s`表中大于等于`Scores`表中数据元素的个数。



### 总结 ###

该查询重点在于建立业务的分析模型，提取不重复数据，然后进行表连接操作。


