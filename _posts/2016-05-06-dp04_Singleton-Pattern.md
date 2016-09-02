---
layout: post
title: "设计模式04_单例模式（Singleton Pattern）"
date: 2016-05-05 23:06:05
categories: DesignPattern
tags: 单例模式
---

* content
{:toc}
 
# 描述 #

 它提供了创建对象的最佳方式。这种模式涉及到一个单一的类，该类负责创建自己的对象，同时确保只有单个对象被创建。这个类提供了一种访问其唯一的对象的方式，可以直接访问，不需要实例化该类的对象。
 




 
**注意 ：**

 - 单例类只能有一个实例。

 - 单例类必须自己创建自己的唯一实例。

 - 单例类必须给所有其他对象提供这一实例。

# 简介 #

##  意图 ##

  保证一个类仅有一个实例，并提供一个访问它的全局访问点。

##  主要解决 ##

  一个全局使用的类频繁地创建与销毁。

##  何时使用 ##

  当想控制实例数目，节省系统资源的时候。

##  解决方案 ##

  判断系统是否已经有这个单例，如果有则返回，如果没有则创建。

##  关键代码 ##

  构造函数是私有的。

##  应用实例 ##

  - 一个党只能有一个主席。

  - Windows 是多进程多线程的，在操作一个文件的时候，就不可避免地出现多个进程或线程同时操作一个文件的现象，所以所有文件的处理必须通过唯一的实例来进行。
  - 一些设备管理器常常设计为单例模式，比如一个电脑有两台打印机，在输出的时候就要处理不能两台打印机打印同一个文件。

## 使用场景 ##

- 要求生产唯一序列号。
- WEB 中的计数器，不用每次刷新都在数据库里加一次，用单例先缓存起来。
- 创建的一个对象需要消耗的资源过多，比如 I/O 与数据库的连接等。

## 注意事项 ##

  getInstance() 方法中需要使用同步锁 synchronized (Singleton.class) 防止多线程同时进入造成 instance 被多次实例化。

## 特点 ##

### 优点 ###

 - 在内存里只有一个实例，减少了内存的开销，尤其是频繁的创建和销毁实例（比如网站首页页面缓存）。

 - 避免对资源的多重占用（比如写文件操作）。

###  缺点 ###

  没有接口，不能继承，与单一职责原则冲突，一个类应该只关心内部逻辑，而不关心外面怎么样来实例化。

# UML #

![单例模式](http://7xucao.com1.z0.glb.clouddn.com/dp04.png)

- 创建一个 SingleObject 类。SingleObject 类有它的私有构造函数和本身的一个静态实例。
- SingleObject 类提供了一个静态方法，供外界获取它的静态实例。
- 使用 SingleObject 类来获取 SingleObject 对象。

# 实现 #

 //步骤1 创建Singleton类
    
     class SingleObject
     {
     private:
     //创建 SingleObject 的一个对象
     static SingleObject instance;
     //让构造函数为 private，这样该类就不会被实例化
     SingleObject(){}
     public:
     //获取唯一可用对象
     static SingleObject getInstance()
     {
     return instance;
     }
     void showMessage()
     {
     cout << "SingleObject Message." << endl;
     }
     };

 //步骤2 从 singleton 类获取唯一的对象。
    
     int main()
     {
     //不合法的构造函数
     //编译时错误：构造函数 SingleObject() 是不可见的
     //SingleObject object = new SingleObject();
     
     //获取唯一可用的对象
     SingleObject object = SingleObject::getInstance();
     
     //显示消息
     object.showMessage();
     system("pause");
     return 0;
     }

 //结果
    
     SingleObject Message.
     请按任意键继续. . .

# 方式 #

##  懒汉模式 ##

**描述：**即第一次调用该类实例的时候才产生一个新的该类实例，并在以后仅返回此实例。需要用锁，来保证其线程安全性。

**原因：**多个线程可能进入判断是否已经存在实例的if语句，从而non thread safety。使用double-check来保证thread safety.但是如果处理大量数据时，该锁才成为严重的性能瓶颈。

###   代码 ###

####    1、静态成员实例的懒汉模式： ####

 //步骤1 创建Singleton类
    
     class SingleObject
     {
     private:
     static SingleObject *instance;
     SingleObject(){}
     public:
     //获取唯一可用对象
     //C++0X以后，要求编译器保证内部静态变量的线程安全性，可以不加锁。但C++ 0X以前，仍需要加锁。
     static SingleObject *getInstance();
     void showMessage()
     {
     cout << "SingleObject Message." << endl;
     }
     };
     SingleObject *SingleObject::getInstance()
     {
     if (SingleObject::instance == NULL)
     {
     mtx.lock();// not needed after C++0x
     if (SingleObject::instance == NULL)
     {
     SingleObject::instance = new SingleObject;
     }
     mtx.unlock(); // not needed after C++0x
     }
     return SingleObject::instance;
     }
     SingleObject *SingleObject::instance = NULL;

#### 2、内部静态实例的懒汉模式 ####
    
     class SingleObject
     {
     private:
     SingleObject(){}
     public:
     //获取唯一可用对象
     //C++0X以后，要求编译器保证内部静态变量的线程安全性，可以不加锁。但C++ 0X以前，仍需要加锁。
     static SingleObject getInstance()
     {
     mtx.lock();// not needed after C++0x
     static SingleObject instance;
     mtx.unlock(); // not needed after C++0x
     return instance;
     }
     void showMessage()
     {
     cout << "SingleObject Message." << endl;
     }
     };

##  饿汉式 ##

  **描述：**无论是否调用该类的实例，在程序开始时就会产生一个该类的实例，并在以后仅返回此实例。因为静态实例初始化在程序开始时进入主函数之前就由主线程以单线程方式完成了初始化，由静态初始化实例保证其线程安全性,不必担心多线程问题。故在性能需求较高时，应使用这种模式，避免频繁的锁争夺。

  **代码**

     class SingleObject
     {
     private:
     //创建 SingleObject 的一个对象
     static const SingleObject *instance;
     //让构造函数为 private，这样该类就不会被实例化
     SingleObject(){}
     public:
     //获取唯一可用对象
     static const SingleObject *getInstance()
     {
     return instance;
     }
     void showMessage() const
     {
     cout << "SingleObject Message." << endl;
     }
     };
     const SingleObject *SingleObject::instance=new SingleObject;
