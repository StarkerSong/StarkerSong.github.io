---
layout: post
title: "MySQL数据库(3)-MySQL基准测试"
date: 2016-10-16 19:38:46 +0800
categories: MySQL
tags:  benchmark
---
* content
{:toc}

![](http://i.imgur.com/TxPjy6g.jpg)








### 基准测试目的 ###

通常的目标是掌握系统的行为，如重现某个系统状态或者做新硬件的可靠性测试等。

- 验证系统假设，确认是否符合实际情况。
- 重现系统异常行为，解决异常。
- 测试系统当前运行情况。
- 模拟比当前系统更高的负载，找出系统压力增加而可能遇到的扩展瓶颈。
- 规划未来业务增长。
- 测试应用适应可变环境的能力。
- 测试不同的硬件、软件和操作系统配置。
- 证明新采购的设备是否配置正确。


### 基准测试策略 ###

- 整体测试（集成式） 
- 单独测试（单组件式） 

### 测试指标 ###

- 吞吐量：单位时间内的事务处理数。
- 响应时间或者延迟：测试任务所需的整体时间。
- 并发性：并发性测试需要关注正在工作中的并发操作，或者同时工作中的线程数或者连接数。当并发性增加时，需要测量吞吐量是否下降，响应时间是否变长，如果是这样，应用可能无法处理峰值压力。
- 可扩展性：系统业务压力可能发生变化，测试可扩展性非常必要。

### 基准测试常见错误 ###

- 使用真实数据的子集而不是全集。
- 使用错误的数据分布。
- 使用不真实的分布参数。
- 在多用户场景中，只做单用户的测试。
- 在单服务器上测试分布式应用。
- 与真实用户行为不匹配。
- 反复执行同一个查询。
- 没有检查错误。
- 忽略系统预热过程。
- 使用默认的服务器配置。
- 测试时间太短。


### 总结 ###

没有做过基准测试，建议至少熟悉`sysbench`，先学习使用`oltp`和`fileio`测试。`oltp`基准测试可以方便比较不同系统的性能。




