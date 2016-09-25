---
layout: post
title: "leetcode数据库总结（6）- Employees Earning More Than Their Managers" 
date: 2016-09-25 22:26:16 +0800
categories: MySQL
tags: SQL
---
* content
{:toc}


![](http://i.imgur.com/I2Mk7KV.jpg)















## 181. Employees Earning More Than Their Managers   ##

### 表结构   ###

```sql
+----+-------+--------+-----------+
| Id | Name  | Salary | ManagerId |
+----+-------+--------+-----------+
| 1  | Joe   | 70000  | 3         |
| 2  | Henry | 80000  | 4         |
| 3  | Sam   | 60000  | NULL      |
| 4  | Max   | 90000  | NULL      |
+----+-------+--------+-----------+
```

### 问题描述  ###

`Employee`表包含所有雇员信息，包含经理信息。每位雇员有个Id，同样还有一列为经理的Id。

查询超过经理薪水的雇员信息，在上面的表中，Joe是仅有的一位赚取超过他的经理。


```sql
+----------+
| Employee |
+----------+
| Joe      |
+----------+
```

### 解决方案  ###

这题的解决方案比较简单，只需要进行简单的表关联，然后进行比较薪水大小就可以了。

```sql
# Write your MySQL query statement below

select  e1.Name Employee 
from  Employee e1, Employee e2
where e1.ManagerId=e2.Id and e1.Salary>e2.Salary
```

### 总结 ###

主要知识点为表连接





