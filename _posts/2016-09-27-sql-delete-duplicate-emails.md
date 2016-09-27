---
layout: post
title: "leetcode数据库总结（9）-Delete Duplicate Emails"
date: 2016-09-27 21:13:02 +0800
categories: MySQL
tags: SQL
---
* content
{:toc}

![](http://i.imgur.com/I2Mk7KV.jpg)











## 196. Delete Duplicate Emails ##

### 表结构 ###

```sql
+----+------------------+
| Id | Email            |
+----+------------------+
| 1  | john@example.com |
| 2  | bob@example.com  |
| 3  | john@example.com |
+----+------------------+
```

### 问题描述 ###


删除`Person`表中重复的邮件，只保留唯一一个邮件。

```sql
+----+------------------+
| Id | Email            |
+----+------------------+
| 1  | john@example.com |
| 2  | bob@example.com  |
+----+------------------+
```

### 解决方案 ###

**子查询方式：**

```sql
delete from Person
where Id not in 
(select Id from 
   (select min(Id) as Id 
    from Person 
    group by Email
   ) p
);
```

**表关联方式：**

```sql
delete p1
from Person p1,Person p2
where p1.Email=p2.Email and p1.Id>p2.Id
```

### 总结 ###

之前学习的都是查询操作，本篇主要是删除操作