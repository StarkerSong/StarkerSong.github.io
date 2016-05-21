---
layout: post
title:  "设计模式03_抽象工厂模式（Abstract Factory Pattern）"
date:   2016-05-01 23:06:05
categories: DesignPattern
tags: 创建型模式 抽象工厂模式
---

* content
{:toc} 

# 描述 #

 围绕一个超级工厂创建其他工厂，又称为其他工厂的工厂。在此模式中，接口负责创建相关对象的工厂，不需要显式指定它们的类。每个生成的工厂都能按照工厂模式提供对象。



 

# 简介 #

##  意图 ##

  提供一个创建一系列相关或相互依赖对象的接口，而无需指定它们具体的类。

##  主要解决 ##

  接口选择问题。

##  何时使用 ##

  系统的产品有多于一个的产品族，而系统只消费其中某一族的产品。

##  解决方案 ##

  在一个产品族里面，定义多个产品。

##  关键代码 ##

  在一个工厂里聚合多个同类产品。

##  应用实例 ##

  商务装、时尚装（一系列具体产品）存放在某个衣柜中（具体工厂），而衣柜（具体工厂）都是衣柜类（抽象工厂）

##  使用场景 ##


  - QQ 换皮肤，一整套一起换

  - 生成不同操作系统的程序。

##  注意事项 ##

  产品族难扩展，产品等级易扩展。

## 特点 ##

###  优点 ###

  当一个产品族中的多个对象被设计成一起工作时，它能保证客户端始终只使用同一个产品族中的对象。

###  缺点 ###

  产品族扩展非常困难，要增加一个系列的某一产品，既要在抽象的 Creator 里加代码，又要在具体的里面加代码。

# UML #

![抽象工厂](http://upload-images.jianshu.io/upload_images/1242974-27866a5bc3ec1e53.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

 - 创建 Shape 和 Color 接口和实现这些接口的实体类。
 - 创建抽象工厂类 AbstractFactory。接着定义工厂类 ShapeFactory 和 ColorFactory。
 - 创建一个工厂创造器/生成器类 FactoryProducer。

# 实现 #

 //步骤1 为形状创建一个接口。

     class Shape
     {
     public:
     virtual void draw() = 0;
     };

 //步骤2 创建实现接口的实体类。

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

 //步骤3 为颜色创建一个接口

     class Color
     {
     public:
     virtual void fill() = 0;
     };

 //步骤4 创建接口的实体类
    
     class Red :public Color
     {
     public:
     virtual void fill()
     {
     cout << "Red::fill() method." << endl;
     }
     };
     
     class Green :public Color
     {
     public:
     virtual void fill()
     {
     cout << "Green::fill() method." << endl;
     }
     };
     
     class Blue :public Color
     {
     public:
     virtual void fill()
     {
     cout << "Blue::fill() method." << endl;
     }
     };

 //步骤5 为 Color 和 Shape 对象创建抽象类来获取工厂。

    class AbstractFactory 
    {
     public:
     virtual Color *getColor(string color) = 0;
     virtual Shape *getShape(string shape) = 0;
     };

 //步骤6 创建扩展了 AbstractFactory 的工厂类，基于给定的信息生成实体类的对象

     class ShapeFactory :public AbstractFactory
     {
     public:
     Color *getColor(string color)
     {
     return NULL;
     }
     Shape *getShape(string shapeType)
     {
     if (shapeType == "CIRCLE") return new Circle();
     else if (shapeType == "RECTANGLE") return new Rectangle();
     else if (shapeType == "SQUARE") return new Square();
     return NULL;
     }
     };
     
     class ColorFactory :public AbstractFactory
     {
     public:
     Color *getColor(string color)
     {
     if (color == "RED") return new Red();
     else if (color == "GREEN") return new Green();
     else if (color == "BLUE") return new Blue();
     return NULL;
     }
     Shape *getShape(string shapeType)
     {
     return NULL;
     }
     };

 //步骤7 创建一个工厂创造器/生成器类，通过传递形状或颜色信息来获取工厂。

     class FactoryProducer
     {
     public:
     static AbstractFactory *getFactory(string choice)
     {
     if (choice == "SHAPE") return new ShapeFactory();
     else if (choice == "COLOR") return new ColorFactory();
     return NULL;
     }
     };

 //步骤8 使用 FactoryProducer 来获取 AbstractFactory，通过传递类型信息来获取实体类的对象。

     int main()
     {
     //获取形状工厂
     AbstractFactory *shapeFactory = FactoryProducer::getFactory("SHAPE");
     //获取形状为 Circle 的对象
     Shape *shape1 = shapeFactory->getShape("CIRCLE");
     //调用 Circle 的 draw 方法
     shape1->draw();
     //获取形状为 Rectangle 的对象
     Shape *shape2 = shapeFactory->getShape("RECTANGLE");
     //调用 Rectangle 的 draw 方法
     shape2->draw();
     //获取形状为 Square 的对象
     Shape *shape3 = shapeFactory->getShape("SQUARE");
     //调用 Square 的 draw 方法
     shape3->draw(); 
     
     //获取颜色工厂
     AbstractFactory *colorFactory = FactoryProducer::getFactory("COLOR");
     //获取颜色为 Red 的对象
     Color *color1 = colorFactory->getColor("RED");
     //调用 Red 的 fill 方法
     color1->fill();
     //获取颜色为 Green 的对象
     Color *color2 = colorFactory->getColor("GREEN");
     //调用 Green 的 fill 方法
     color2->fill();
     //获取颜色为 Blue 的对象
     Color *color3 = colorFactory->getColor("BLUE");
     //调用 Blue 的 fill 方法
     color3->fill(); 
     
     system("pause");
     return 0;
     }

 //结果

     Circle::draw method.
     Rectangle::draw() method.
     Square::draw method.
     Red::fill() method.
     Green::fill() method.
     Blue::fill() method.
     请按任意键继续. . .
