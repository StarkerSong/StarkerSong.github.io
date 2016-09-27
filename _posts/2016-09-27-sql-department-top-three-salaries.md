---
layout: post
title: "leetcode数据库总结（9）-Department Top Three Salaries"
date: 2016-09-27 18:57:45 +0800
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
| 5  | Janet | 69000  | 1            |
| 6  | Randy | 85000  | 1            |
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

写个`SQL`查询，查找出各个部门薪水排前三的员工信息。查询结果如下表：

```sql
+------------+----------+--------+
| Department | Employee | Salary |
+------------+----------+--------+
| IT         | Max      | 90000  |
| IT         | Randy    | 85000  |
| IT         | Joe      | 70000  |
| Sales      | Henry    | 80000  |
| Sales      | Sam      | 60000  |
+------------+----------+--------+
```

### 解决方案 ###

建立查询语句重点是要拆分业务逻辑，通过将`Employee e1`表中的薪水与`Employee e2`表中的数据比较统计出排名前列的数据。其中`3`可以改成任意数值，表示求出各个分组中排名前几的数据。

```sql
select d.Name Department ,e1.Name Employee ,e1.Salary 
from Employee e1,Department d
where e1.DepartmentId =d.Id and
3>
(
select count(distinct(e2.Salary)) 
from Employee e2 
where e1.DepartmentId=e2.DepartmentId and e1.Salary<e2.Salary
)
order by d.Id,e1.Salary desc
 
```

### 总结 ###


拆分业务逻辑。





















