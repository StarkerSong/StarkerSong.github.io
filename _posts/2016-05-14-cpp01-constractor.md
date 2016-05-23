---
layout: post
title:  "Cpp Primer_复制控制"
date:   2016-05-14 23:06:05
categories: Cpp
tags: 复制构造函数 赋值操作符 析构函数 智能指针
---
* content
{:toc} 

# 复制构造函数 #

##  基本概念 ##

  只有单个形参，而且该形参是对本类类型对象的引用（常用 const 修饰），与默认构造函数一样，复制构造函数可由编
 译器隐式调用。

      class Foo {
     public:
     Foo(); // default constructor
     Foo(const Foo&); // copy constructor 。因为用于向函数传递对象和从函数返回对象，该构造函数一般不应
     设置为 explicit（
     // ...
     };








##  用途 ##

- 根据另一个同类型的对象显式或隐式初始化一个对象。
- 复制一个对象，将它作为实参传给一个函数。
- 从函数返回时复制一个对象。
- 初始化顺序容器中的元素。
- 根据元素初始化式列表初始化数组元素。

##  合成复制构造函数 ##

  合成复制构造函数的行为是，执行逐个成员初始化，将新对象初始
 化为原对象的副本。

      Sales_item::Sales_item(const Sales_item &orig):
     isbn(orig.isbn), // uses string copy constructor
     units_sold(orig.units_sold), // copies orig.units_sold
     revenue(orig.revenue) // copy orig.revenue
     { } // empty body

# 赋值操作符 #

##  概念 ##

  赋值操作符可以通过指定不同类型的右操作数而重载。右操作数为类类型的版本比较特殊：如果我们没有编写这种版本，编译器将为我们合成一个。

      class Sales_item {
     public:
     // other members as before
     // equivalent to the synthesized assignment operator
     Sales_item& operator=(const Sales_item &);
     };

##  合成赋值操作符 ##

  合成赋值操作符与合成复制构造函数的操作类似。它会执行逐个成员赋值：右操作数对象的每个成员赋值给左操作数对象的对应成员。除数组之外，每个成员用所属类型的常规方式进行赋值。对于数组，给每个数组元素赋值。

      // equivalent to the synthesized assignment operator
     Sales_item&
     Sales_item::operator=(const Sales_item &rhs)
     {
     isbn = rhs.isbn; // calls string::operator=
     units_sold = rhs.units_sold; // uses built-in int assignment
     revenue = rhs.revenue; // uses built-in double
     assignment
     return *this;
     }

# 析构函数 #

##  概念 ##

  析构函数可用于释放对象时构造或在对象的生命期中所获取的资源。不管类是否定义了自己的析构函数，编译器都自动执行类中非static 数据成员的析构函数。

##  合成析构函数 ##

  合成析构函数按对象创建时的逆序撤销每个非 static 成员，因此，它按成员在类中声明次序的逆序撤销成员。对于类类型的每个成员，合成析构函数调用该成员的析构函数来撤销对象。撤销内置类型成员或复合类型的成员没什么影响。尤其是，合成析构函数并不删除指针成员所指向的对象。

  与复制构造函数和赋值操作符不同，无论类是否定义了自己的析构函数，都会创建和运行合成析构函数。如果类定义了析构函数，则在类定义的析构函数结束之后运行合成析构函数。

# 管理指针成员 #

##  控制策略 ##

 1. 指针成员采取**常规指针型**行为。这样的类具有指针的所有缺陷但无需特殊的复制控制。
 2. 类可以实现所谓的**“智能指针”**行为。指针所指向的对象是共享的，但类能够防止悬垂指针。
 3. 类采取**值型**行为。指针所指向的对象是唯一的，由每个类对象独立管理。

##  使用计数(Use count) ##

  使用计数是复制控制成员中使用的编程技术。将一个计数器与类指向的对象相关联用于跟踪该类多少个对象共享同一个指针。创建一个单独类指向共享对象并管理使用计数。由构造函数设置共享对象的状态并将使用计数置为1。每当由复制构造函数或复制操作符生成一个新副本时，使用计数加1。由析构函数撤销对象或作为赋值操作符的左操作数撤销对象时，使用计数减1。赋值操作符和析构函数检查使用计数是否减至0，如果是，则撤销对象。

      // private class for use by HasPtr only
     class U_Ptr {
     friend class HasPtr;
     int *ip;
     size_t use;
     U_Ptr(int *p): ip(p), use(1) { }
     ~U_Ptr() { delete ip; }
     };

![创建一个 HasPtr 对象](http://7xucao.com1.z0.glb.clouddn.com/HasPtr1.png)

  
![复制对象](http://7xucao.com1.z0.glb.clouddn.com/HasPtr2.png)


  

##  智能指针 ##

  智能指针是行为类似指针但也提供其他功能的类。

  智能指针类实现与普通指针行为的类的区别在于：智能指针通常接受指向动态分配对象的指针并负责删除该对象。用户分配对象，但由智能指针删除它，因此智能指针需要实现复制控制成员来管理指向共享对象的指针。只有在撤销了指向共享对象的最后一个指针后，才能删除该共享对象。使用计数是实现智能指针最常用的方式。

##  值类型 ##

  具有值语义的类，其特征为：对该类对象进行复制时，会得到一个不同的新副本，对副本所做的改变不会影响原有对象。