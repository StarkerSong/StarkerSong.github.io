---
layout: post
title: "leetcode数据库总结（3）-Nth Highest Salary"
date: 2016-09-22 20:31:17 +0800
categories: MySQL
tags: SQL
---
* content
{:toc}

![](http://i.imgur.com/I2Mk7KV.jpg)












## 177. Nth Highest Salary ##



### 表结构 ###



```sql
+----+--------+
| Id | Salary |
+----+--------+
| 1  | 100    |
| 2  | 200    |
| 3  | 300    |
+----+--------+
```

### 问题描述 ###

写一个SQL查询从`Employee`表中获取薪水第`N`高的数据。例如在上面的表中，薪水第二高的数据为200，如果没有第`N`高的薪水，那么就应该返回`null`。

### 解决方案 ###

这题的思路和前一个问题的思路是一致的重点在于对`SQL`函数的熟悉。

```sql
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN 
    set N=N-1;
    -- return   (select distinct Salary from Employee order by Salary desc limit N,1);

    return (
     select  
         (
            select Salary from Employee 
            union
            select null
            order by Salary desc limit N,1
         )
    );
END
```

### 总结 ###

本篇内容除了要了解上篇的内容，还需要了解创建函数的方法。还需要注意的是数据库中的索引是从`0`开始的，所以在查询时需要对计数`N`减一。

```sql
-- 第一个参数指定第一个返回记录行的偏移量
-- 第二个参数指定返回记录行的最大数目
SELECT * FROM table  LIMIT [offset,] rows | rows OFFSET offset

SELECT * FROM table LIMIT 95,-1; -- 检索记录行 96-last.
```



