---
layout: post
title:  "设计模式01_简介（Introduction）"
date:   2016-04-31 23:06:05
categories: DesignPattern
tags: 创建型模式 结构型模式 行为模式
---
* content
{:toc}


![摘要](http://upload-images.jianshu.io/upload_images/1242974-db23bec89ba568d5.png) 

# 概念 #

 软件开发人员在软件开发过程中面临一般问题的解决方案。






# 面向对象设计原则 #


![设计原则](http://upload-images.jianshu.io/upload_images/1242974-56230fe31338abd0.png)


# 分类 #

 ## 创建型模式 ##

  描述：创建对象同时隐藏创建逻辑的方式，而不是使用新的运算符直接实例化对象。使程序在判断针对给定实例创建对象时更灵活。

- 工厂模式（Factory Pattern）
- 抽象工厂模式（Abstract Factory Pattern）
- 单例模式（Singleton Pattern）
- 建造者模式（Builder Pattern）
- 原型模式（Prototype Pattern）

##  结构型模式 ##

  描述：关注类和对象的组合。集成的概念被用来组合接口和定义组合对象获得新功能的方式。

- 适配器模式（Adapter Pattern）
- 桥接模式（Bridge Pattern）
- 过滤器模式（Filter、Criteria Pattern）
- 组合模式（Composite Pattern）
- 装饰器模式（Decorator Pattern）
- 外观模式（Facade Pattern）
- 享元模式（Flyweight Pattern）
- 代理模式（Proxy Pattern）

##  行为模式 ##

 描述：关注对象间的通信。

- 责任链模式（Chain of Responsibility Pattern）
- 命令模式（Command Pattern）
- 解释器模式（Interpreter Pattern）
- 迭代器模式（Iterator Pattern）
- 中介者模式（Mediator Pattern）
- 备忘录模式（Memento Pattern）
- 观察者模式（Observer Pattern）
- 状态模式（State Pattern）
- 空对象模式（Null Object Pattern）
- 策略模式（Strategy Pattern）
- 模板模式（Template Pattern）
- 访问者模式（Visitor Pattern）

# 关系 #

 
![设计模式间的关系](http://upload-images.jianshu.io/upload_images/1242974-09e0893682a95b26.png)


# 六大原则 #


![六大原则](http://upload-images.jianshu.io/upload_images/1242974-4a6953c99be09096.png)


##  开闭原则（Open Close Principle） ##

  对扩展开放，对修改关闭。使程序的扩展性好，易于维护和升级。

##  里氏代换原则（Liskov Substitution Principle） ##

  面向对象设计的基本原则之一。任何基类可以出现的地方，子类一定可以出现。LSP 是继承复用的基石，只有当派生类可以替换掉基类，
 且软件单位的功能不受到影响时，基类才能真正被复用，而派生类也能够在基类的基础上增加新的行为。里氏代换原则是对开闭原则的补充。
 实现开闭原则的关键步骤就是抽象化，而基类与子类的继承关系就是抽象化的具体实现，所以里氏代换原则是对实现抽象化的具体步骤的规范。

##  依赖倒转原则（Dependence Inversion Principle） ##

  针对接口编程，依赖抽象而不依赖具体实现。此原则是开闭原则的基础。

##  接口隔离原则（Interface Segregation Principle） ##

  降低类之间的耦合度。从大型软件设计架构，便于升级和维护的软件设计思想出发，强调降低依赖，降低耦合。

##  迪米特法则，又称最少知道原则（Demeter Principle） ##

  实体尽量减少与其他实体之间发生相互作用，使系统功能模块相对独立。

##  合成复用原则（Composite Reuse Principle） ##

  尽量使用合成/聚合的方式而不是使用继承。
