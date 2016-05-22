---
layout: post
title: "设计模式05_建造者模式（Builder Pattern）"
date: 2016-05-07 23:06:05
categories: DesignPattern
tags: 创建型模式 建造者模式
---

* content
{:toc} 

# 描述 #

 该模式使用多个简单的对象一步一步构建成一个复杂的对象。一个 Builder 类会一步一步构造最终的对象。该 Builder 类是独立于其他对象的。



 

# 简介 #

##  意图 ##

  将一个复杂的构建与其表示相分离，使得同样的构建过程可以创建不同的表示。

##  主要解决 ##

  主要解决在软件系统中，复杂对象的创建工作。由于需求的变化，复杂对象的各个部分经常面临剧烈的变化，但将它们组合在一起却相对稳定。

##  何时使用 ##

  基本部件不变，其组合经常变化的时候。

##  解决方案 ##

  将变与不变分离。

##  关键代码 ##

  建造者：创建和提供实例

导演：管理建造出来的实例的依赖关系。

##  应用实例 ##

  去肯德基，汉堡、可乐、薯条、炸鸡翅等是不变的，而其组合是经常变化的，生成出所谓的"套餐"。

##  使用场景 ##

  - 需要生成的对象具有复杂的内部结构。

  - 需要生成的对象内部属性本身相互依赖。

##  注意事项 ##

  与工厂模式的区别是：建造者模式更加关注与零件装配的顺序。

## 特点 ##

###  优点 ###

 -  建造者独立，易扩展。

-  便于控制细节风险。

###  缺点 ###

-  产品必须有共同点，范围有限制。
- 需要生成的对象内部属性本身相互依赖。

# UML #

![建造者模式](http://upload-images.jianshu.io/upload_images/1242974-74be3ce2d3e59e85.png)

- 纸盒中 --汉堡（Burger）--素食汉堡（Veg Burger）或鸡肉汉堡（Chicken Burger）瓶子中 --冷饮（Cold drink）--可口可乐（coke）或百事可乐（pepsi）
- 创建一个表示食物条目（比如汉堡和冷饮）的 Item 接口和实现 Item 接口的实体类，以及一个表示食物包装的 Packing 接口和实现 Packing 接口的实体类，汉堡是包在纸盒中，冷饮是装在瓶子中。
- 创建一个 Meal 类，带有 Item 的 ArrayList 和一个通过结合 Item 来创建不同类型的 Meal 对象的 MealBuilder。
- 使用 MealBuilder 来创建一个 Meal。

# 实现

 //步骤1 创建一个表示食物条目和食物包装的接口。
    
     class Packing
     {
     public:
     virtual string pack() = 0;
     };
     class Item
     {
     public:
     virtual string name() = 0;
     virtual Packing *packing() = 0;
     virtual float price() = 0;
     };

 //步骤2 创建实现 Packing 接口的实体类。
    
     class Wrapper :public Packing //包装盒
     {
     public:
     virtual string pack()
     {
     return "Wrapper";
     }
     };
     
     class Bottle :public Packing //饮料瓶
     {
     public:
     virtual string pack()
     {
     return "Bottle";
     }
     };

 //步骤3 创建实现 Item 接口的抽象类，该类提供了默认的功能。
    
     class Burger :public Item
     {
     public:
     Packing *packing()
     {
     return new Wrapper();
     }
     };
     
     class ColdDrink :public Item
     {
     public:
     Packing *packing()
     {
     return new Bottle();
     }
     };

 //步骤4 创建扩展了 Burger 和 ColdDrink 的实体类。
    
     class VegBurger :public Burger
     {
     public:
     float price(){ return 15.0f; }
     string name(){ return "VegBurger"; }
     };
     class ChickenBurger :public Burger
     {
     public:
     float price(){ return 35.0f; }
     string name(){ return "ChickenBurger "; }
     };
     
     class Coke :public ColdDrink
     {
     public:
     float price(){ return 10.0f; }
     string name(){ return "Coke"; }
     };
     class Pepsi :public ColdDrink
     {
     public:
     float price(){ return 20.0f; }
     string name(){ return "Pepsi"; }
     };

 //步骤5 创建一个 Meal 类，带有上面定义的 Item 对象。
    
     class Meal
     {
     public:
     void addItem(Item *item)
     {
     items.push_back(item);
     }
     float getCost()
     {
     float cost = 0.0f;
     for (vector<Item *>::iterator iter = items.begin(); iter != items.end(); ++iter)
     {
     cost += (*iter)->price();
     }
     return cost;
     }
     void showItems()
     {
     for (vector<Item *>::iterator iter = items.begin(); iter != items.end(); ++iter)
     {
     cout << "Item:" << (*iter)->name() << " Packing:" << (*iter)->packing()->pack() << " Price:" << (*iter)->price() << endl;
     }
     }
     private:
     vector<Item *> items;
     };

 //步骤6 创建一个 MealBuilder 类，实际的 builder 类负责创建 Meal 对象。
    
     class MealBuilder
     {
     public:
     Meal *prepareVegMeal()
     {
     Meal *meal = new Meal();
     meal->addItem(new VegBurger());
     meal->addItem(new Coke());
     return meal;
     }
     Meal *prepareNonVegMeal()
     {
     Meal *meal = new Meal();
     meal->addItem(new ChickenBurger());
     meal->addItem(new Pepsi());
     return meal;
     }
     };

 //步骤7 BuiderPatternDemo 使用 MealBuider 来演示建造者模式（Builder Pattern）。
    
     int main()
     {
     MealBuilder *mealBuilder = new MealBuilder();
     Meal *vegMeal = mealBuilder->prepareVegMeal();
     cout << "vegMeal" << endl;
     vegMeal->showItems();
     cout << "Total Cost:" << vegMeal->getCost() << endl;
     
     Meal *nonVegMeal = mealBuilder->prepareNonVegMeal();
     cout << "\n\nNonVegMeal" << endl;
     nonVegMeal->showItems();
     cout << "Total Cost:" << nonVegMeal->getCost() << endl;
     
     system("pause");
     return 0;
     }
