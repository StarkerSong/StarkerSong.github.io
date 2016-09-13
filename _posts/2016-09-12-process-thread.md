---
layout: post
title: "在系统中引入进程和线程的深度解析"
date: 2016-09-12 21:13:29 +0800
categories: Basics
tags: thread process
---
* content
{:toc}

![](http://i.imgur.com/jVKVAn3.jpg)







在操作系统或者程序开发中经常遇到进程和线程的问题，但是许多人包括我自己在很多时候并没有详细了解为什么要引入进程和线程？进程和线程各自组成和属性是什么？在应用中是如何解决问题的？带着问题去理解书本上生涩的概念知识将别有一番境地。

在早期的计算机上，只能按照指定的顺序执行，所处理的事情只能串行逐个完成，后来人们将计算机处理事情的时间分成一段一段的，模拟处理多个不同的事情，由于处理时间的建个特别短，看起来好像在同时处理。但是如果处理的事情不是相互独立的，存在某种关系，例如共同占用一些资源，那么对于这些事情处理的不同顺序将会带来不同的结果。

由于程序在并发执行时，它们将失去封闭性，并且可能造成的执行结果的不可再现性。通过引入进程来描述程序的并发执行，对进程必要的管理，以便更好地描述和控制程序的并发执行，这样就可以实现操作系统的并发性和共享性。

（未完）



## 参考资料 ##

- [为什么要在操作系统中引入线程？](http://blog.csdn.net/wangshihui512/article/details/9897607)http://blog.csdn.net/wangshihui512/article/details/9897607
- [进程概念的引入](http://www.voidcn.com/blog/haiross/article/p-3165446.html)http://www.voidcn.com/blog/haiross/article/p-3165446.html



