---
layout: post
title: "设计模式06_原型模式（Prototype Pattern）"
date: 2016-05-08 23:06:05
categories: DesignPattern
tags: 创建型模式 原型模式
---

* content
{:toc} 

# 描述 #

 用于创建重复的对象，同时又能保证性能。实现了一个原型接口，该接口用于创建当前对象的克隆。当直接创建对象的代价比较大时，则采用这种模式。
 例如，一个对象需要在一个高代价的数据库操作之后被创建。我们可以缓存该对象，在下一个请求时返回它的克隆，在需要的时候更新数据库，以此来减少数据库调用。




 

# 简介 #

##  意图 ##

  用原型实例指定创建对象的种类，并且通过拷贝这些原型创建新的对象。

##  主要解决 ##

  在运行期建立和删除原型。

##  何时使用 ##

- 要实例化的类是在运行时刻指定时，例如，通过动态装载。
- 为了避免创建一个与产品类层次平行的工厂类层次时。
- 当一个系统应该独立于它的产品创建，构成和表示时。
- 当一个类的实例只能有几个不同状态组合中的一种时。建立相应数目的原型并克隆它们可能比每次用合适的状态手工实例化该类更方便一些。

  
##  解决方案 ##

  利用已有的一个原型对象，快速地生成和原型对象一样的实例。

##  关键代码 ##

  - 实现克隆操作，在 JAVA 继承 Cloneable，重写 clone()，在 .NET 中可以使用 Object 类的 MemberwiseClone() 方法来实现对象的浅拷贝或通过序列化的方式来实现深拷贝。

  - 原型模式同样用于隔离类对象的使用者和具体类型（易变类）之间的耦合关系，它同样要求这些"易变类"拥有稳定的接口。

##  应用实例 ##

  - 细胞分裂。

  - JAVA 中的 Object clone() 方法。

##  使用场景 ##

- 资源优化场景。
- 类初始化需要消化非常多的资源，这个资源包括数据、硬件资源等。
- 性能和安全要求的场景。
- 通过 new 产生一个对象需要非常繁琐的数据准备或访问权限，则可以使用原型模式。
- 一个对象多个修改者的场景。
- 一个对象需要提供给其他对象访问，而且各个调用者可能都需要修改其值时，可以考虑使用原型模式拷贝多个对象供调用者使用。
- 在实际项目中，原型模式很少单独出现，一般是和工厂方法模式一起出现，通过 clone 的方法创建一个对象，然后由工厂方法提供给调用者。

##  注意事项 ##

  与通过对一个类进行实例化来构造新对象不同的是，原型模式是通过拷贝一个现有对象生成新对象的。浅拷贝实现 Cloneable，重写，深拷贝是通过实现 Serializable 读取二进制流。

## 特点 ##

###  优点 ###

  - 性能提高。

  - 逃避构造函数的约束。

###  缺点 ###

 - 配备克隆方法需要对类的功能进行通盘考虑，这对于全新的类不是很难，但对于已有的类不一定很容易，特别当一个类引用不支持串行化的间接对象，或者引用含有循环结构的时候。

 - 必须实现 Cloneable 接口。

 - 逃避构造函数的约束。

# UML #

![原型模式](http://upload-images.jianshu.io/upload_images/1242974-bbdc7d5b719a71ef.png)

 ● 创建一个抽象类 Prototype 和扩展了Prototype 类的实体类
 ● 使用Client来获取 Prototype 对象。

# 实现 #

 //步骤1 创建一个实现了 Prototype 接口的抽象类。

     class Prototype
     {
     public:
     virtual Prototype *clone() = 0;
     virtual void showMessage() = 0;
     };

 //步骤2 创建ConcretePrototype实体类

     class ConcretePrototype :public Prototype
     {
     public: 
     //构造函数
     ConcretePrototype() :m_counter(0){}
     //浅拷贝 如果涉及指针变量成员分配空间，需要深拷贝
     ConcretePrototype(const ConcretePrototype &conpro)
     {
     m_counter = conpro.m_counter;
     }
     //复制自身
     virtual Prototype *clone()
     {
     //调用拷贝构造函数
     return new ConcretePrototype(*this);
     } 
     //显示输出
     void showMessage()
     {
     cout << "ConcretePrototype showMessage method." << endl;
     }
     private:
     int m_counter;
     };

 //步骤3 完成克隆

     int main()
     {
     Prototype *concretPrototype1 = new ConcretePrototype;
     Prototype *concretPrototype2 = concretPrototype1->clone();
     concretPrototype2->showMessage();
     
     delete concretPrototype1;
     concretPrototype1 = NULL;
     delete concretPrototype2;
     concretPrototype2 = NULL;
     system("pause");
     return 0;
     }