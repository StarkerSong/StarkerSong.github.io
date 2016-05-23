---
layout: post
title:  "设计模式02_工厂模式（Factory Pattern）"
date:   2016-05-01 23:06:05
categories: DesignPattern
tags: 创建型模式 工厂模式
---

* content
{:toc}

 创建对象时不会对客户端暴露创建逻辑，并且是通过使用一个共同的接口来指向新创建的对象。




 

# 描述 #

 创建对象时不会对客户端暴露创建逻辑，并且是通过使用一个共同的接口来指向新创建的对象。

# 简介 #

##  意图 ##

  定义创建对象的接口，让子类决定实例化那一个工厂类，工厂模式使其创建过程延迟到子类进行。

##  主要解决 ##

  接口选择问题。

##  何时使用 ##

  明确计划不同条件下创建不同实例时。
## 解决方案 ##

  让子类实现工厂接口，返回抽象产品。

## 关键代码 ##

  创建过程在其子类执行。

##  应用实例 ##

  - 使用手机而不关心其具体实现

  - Hibernate 换数据库只需换方言和驱动就可以。

##  使用场景 ##

![使用场景](http://7xucao.com1.z0.glb.clouddn.com/dp02.png)


 -  日志记录器：记录可能记录到本地硬盘、系统事件、远程服务器等，用户可以选择记录日志到什么地方。

 - 数据库访问，当用户不知道最后系统采用哪一类数据库，以及数据库可能有变化时。

 - 设计一个连接服务器的框架，需要三个协议，"POP3"、"IMAP"、"HTTP"，可以把这三个作为产品类，共同实现一个接口。

##  注意事项 ##

  复杂对象适合使用工厂模式，而简单对象，特别是只需要通过 new 就可以完成创建的对象，无需使用工厂模式。

## 特点 ##

###  优点 ###

- 创建对象只需知道其名称
- 扩展性高，增加产品，只需扩展工厂类。
- 屏蔽产品具体实现，调用者只管新产品的接口。

###  缺点 ###

  每增加产品都需要增加具体类和对象实现工厂，使系统中的对象成倍增加。增加了系统的复杂度和具体类的依赖。

# UML #

 

![工厂模式](http://7xucao.com1.z0.glb.clouddn.com/dp022.png)

- 创建一个 Shape 接口和实现 Shape 接口的实体类。
- 定义工厂类 ShapeFactory。
- FactoryPatternDemo，演示类使用 ShapeFactory 来获取 Shape 对象。它将向 ShapeFactory 传递信息（CIRCLE / RECTANGLE / SQUARE），以便获取它所需对象的类型。

# 实现 #

 //步骤1 创建一个接口

```cpp
     class Shape
     {
     public:
     virtual void draw()=0;
     };
```
 //步骤2 创建实现接口的实体类

```
     class Rectangle :public Shape
     {
     public:
     virtual void draw()
     {
     cout << "Rectangle::draw() method." << endl;
     }
     };
     
     class Square :public Shape
     {
     public:
     virtual void draw()
     {
     cout << "Square::draw method." << endl;
     }
     };
     
     class Circle :public Shape
     {
     public:
     virtual void draw()
     {
     cout << "Circle::draw method." << endl;
     }
     };
```

 //步骤3 创建一个工厂，生成基于给定信息的实体类的对象。

```
     class ShapeFactory
     {
     public:
     Shape *getShape(string shapeType)
     {
     if (shapeType == "CIRCLE") return new Circle();
     else if (shapeType == "RECTANGLE") return new Rectangle();
     else if (shapeType == "SQUARE") return new Square();
     return NULL;
     }
     };
```

 //步骤4 使用该工厂，通过传递类型信息来获取实体类的对象。

```
     int main()
     {
     ShapeFactory *shapeFactory = new ShapeFactory();
     //获取 Circle 的对象，并调用它的 draw 方法
     Shape *shape1 = shapeFactory->getShape("CIRCLE");
     //调用 Circle 的 draw 方法
     shape1->draw();
     //获取 Rectangle 的对象，并调用它的 draw 方法
     Shape *shape2 = shapeFactory->getShape("RECTANGLE");
     //调用 Rectangle 的 draw 方法
     shape2->draw();
     //获取 Square 的对象，并调用它的 draw 方法
     Shape *shape3 = shapeFactory->getShape("SQUARE");
     //调用 Square 的 draw 方法
     shape3->draw();
     system("pause");
     return 0;
     }
```

 //结果

```
     Circle::draw method.
     Rectangle::draw() method.
     Square::draw method.
     请按任意键继续. . .
```
