---
layout: post
title: "leetcode数据库总结（2）-Second Highest Salary"
date: 2016-09-21 22:54:55 +0800
categories: SQL
tags: SQL
---
* content
{:toc}

![](http://i.imgur.com/I2Mk7KV.jpg)











## 176. Second Highest Salary ##

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

写一个SQL查询从`Employee`表中获取薪水第二高的数据。例如在上面的表中，薪水第二高的数据为200，如果没有第二高的薪水，那么就应该返回`null`。

### 解决方案 ###

我的想法是`Employee`表中的`Salary`数据与`null`并起来，经过排序后`null`将会排在最后面，所以接下来就可以使用`limit`了。

**时间性能：**907 ms

```sql
 select  
 (
    select Salary from Employee 
    union
    select null
    order by Salary desc limit 1,1
 )
 SecondHighestSalary
```

在上面的想法上是考虑了薪水存在相同的情况，但是我看到`leetcode`中许多人的解法都没有考虑这种情况，所以又有下面的一些解法。


**时间性能：**612 ms

```sql
select max(salary) SecondHighestSalary from Employee where salary !=(select max(salary) from Employee)
```

**时间性能：**645 ms

```sql
select 
(
select distinct Salary 
from Employee 
order by Salary 
desc limit 1,1
) SecondHighestSalary

```


### 总结 ###

在上面的题目中，我们首先要了解题意，才能给出更优的性能查询语句。在不考虑重复数据的情况下，通过使用聚集函数的效率更高。
涉及知识点有如下方面：

- 聚集函数（max、min、sum、count、avg）
- distinct
- union





