---
layout: post
title: "leetcode数据库总结（9）-Department Highest Salary
date: 2016-09-27 17:39:51 +0800
categories: MySQL
tags: SQL
---
* content
{:toc}

![](http://i.imgur.com/I2Mk7KV.jpg)










## 182. Department Highest Salary ##

### 表结构 ###


表：`Employee`

```sql
+----+-------+--------+--------------+
| Id | Name  | Salary | DepartmentId |
+----+-------+--------+--------------+
| 1  | Joe   | 70000  | 1            |
| 2  | Henry | 80000  | 2            |
| 3  | Sam   | 60000  | 2            |
| 4  | Max   | 90000  | 1            |
+----+-------+--------+--------------+
```

表：`Department`

```sql
+----+----------+
| Id | Name     |
+----+----------+
| 1  | IT       |
| 2  | Sales    |
+----+----------+
```

### 问题描述 ###

写`SQL`语句查询出各个部门最高薪水的员工，在上述的表中，`Max`在IT部门薪水最高，`Henry`在销售部门的薪水最高。


```sql
+------------+----------+--------+
| Department | Employee | Salary |
+------------+----------+--------+
| IT         | Max      | 90000  |
| Sales      | Henry    | 80000  |
+------------+----------+--------+
```

### 解决方案 ###

在上篇中讲过，能不用子查询就尽量不要用子查询，下面的方法先查询出各个部门的最高工资的数值，然后再进行表连接操作。

```sql
select de.Name Department ,e.Name Employee , e.Salary
from Employee e,Department de,
(select DepartmentId ,max(Salary) Salary
from Employee
group by DepartmentId
) d
where e.DepartmentId=d.DepartmentId  and de.Id=e.DepartmentId and e.Salary>=d.Salary
```


### 总结 ###

主要涉及子查询和表连接问题。


