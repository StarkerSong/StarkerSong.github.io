---
layout: post
title: "leetcode数据库总结（7）-Duplicate Emails"
date: 2016-09-26 21:45:09 +0800
categories: MySQL
tags: SQL
---
* content
{:toc}

![](http://i.imgur.com/I2Mk7KV.jpg)













## 182. Duplicate Emails ##

### 表结构 ###

```sql
+----+---------+
| Id | Email   |
+----+---------+
| 1  | a@b.com |
| 2  | c@d.com |
| 3  | a@b.com |
+----+---------+
```

### 问题描述 ###

写一个`SQL`查询，查找到`Person`表中所有重复的邮件。例如在上面的表中的查询结果为：

```sql
+---------+
| Email   |
+---------+
| a@b.com |
+---------+
```


### 解决方案 ###

```sql
select Email 
from Person 
group by Email 
having count(Email)>1
```

### 总结 ###

这篇的查询首先想到的就是根据分组以及组内的邮件数量来判断是否重复。
