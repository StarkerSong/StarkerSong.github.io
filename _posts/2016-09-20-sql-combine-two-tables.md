---
layout: post
title: "leetcode数据库总结（1）-Combine Two Tables"
date: 2016-09-20 23:27:20 +0800
categories: MySQL
tags: SQL
---
* content
{:toc}

![](http://i.imgur.com/I2Mk7KV.jpg)









## 175. Combine Two Tables ##

### 表结构 ###

表结构：`Person`

```sql
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| PersonId    | int     |
| FirstName   | varchar |
| LastName    | varchar |
+-------------+---------+
PersonId 是表的主键。
```

表结构：`Address`

```sql
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| AddressId   | int     |
| PersonId    | int     |
| City        | varchar |
| State       | varchar |
+-------------+---------+
AddressId是主键
```

### 问题描述 ###

写`SQL`查询提供以下字段信息，不管那些有没有地址的人。

```sql
FirstName, LastName, City, State
```

### 解决方案 ###

根据问题描述，可以知道可以知道需要用`left join`解决上述问题。

```sql
select Person.FirstName, Person.LastName, Address.City, Address.State 
from Person 
left join Address 
on Address.PersonId =Person.PersonId 
```

### 总结 ###

以上主要涉及到以下知识点：

- `inner join`
- `left join`
- `right join`
- `full join`

通过图表显示会更加直观。

![](http://i.imgur.com/iWjU2UK.png)