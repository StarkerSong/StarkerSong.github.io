---
layout: post
title: "STL-Vector"
date: 2016-08-07 13:07:19 +0800
categories: C++
tags: vector STL
---
* content
{:toc}

![](http://i.imgur.com/5R4UQQB.jpg)

在`C++`中经常会用到`vector`容器，基本内容如下：







## 基本操作 ##

- 头文件 	`#include <vector>`
- 创建vector对象 `vector<int> vec`
- 尾部插入数字 `vec.push_back(a)`
- 使用下标访问元素`cout<<vec[0]<<endl;`记住下标是从0开始的。

### 访问元素  ###

```cpp

//记住下标是从0开始的。
cout<<vec[0]<<endl;

//at访问元素
for (unsigned i=0; i<myvector.size(); i++)
	std::cout << ' ' << myvector.at(i);

//反向迭代器访问元素
std::vector<int>::reverse_iterator rit = myvector.rbegin();
for (; rit!= myvector.rend(); ++rit)
	*rit = ++i;

//front() 和back()访问
 myvector.front() -= myvector.back();
  std::cout << "myvector.front() is now " << myvector.front() << '\n';

```

- 插入元素  `vec.insert(vec.begin()+i,a);`在第i+1个元素前面插入a;
- 删除元素  `vec.erase(vec.begin()+2);`删除第3个元素     
     `vec.erase(vec.begin()+i,vec.end()+j);`删除区间[i,j-1];区间从0开始
	 `vec.pop_back();`删除最后一个元素

### 初始化和赋值 ###

```cpp
void assign( input_iterator start, input_iterator end ); //
void assign( size_type num, const TYPE &val );
```

定义空二维vector，再赋值

```cpp
vector<vector <int> > ivec(m ,vector<int>(n)); //m*n的二维vector，注意两个 "> "之间要有空格！
void assign(const_iterator first, const_iterator last);
void assign( size_type _Count, const Type& _Val ); // 赋值，用指定元素序列替换容器内所有元素
```

定义并初始化二维数组

```cpp
vector<vector <int> > ivec(m ,vector<int>(n,0)); //m*n的二维vector，所有元素初始化为0
```


### 删除元素 ###

```cpp
iterator erase(iterator it); // 删除指定元素，并返回删除元素后一个元素的位置（如果无元素，返回end()）
iterator erase(iterator first, iterator last); // 注意：删除元素后，删除点之后的元素对应的迭代器不再有效。

void clear() const; // 函数删除当前vector中的所有元素，相当于调用erase( begin(), end())

```

### 容量 ###

```cpp
vec.reserve(100)

std::vector<int> myvector;

// set some initial content:
for (int i=1;i<10;i++) myvector.push_back(i);

myvector.resize(5);
myvector.resize(8,100);
myvector.resize(12);

std::cout << "myvector contains:";
for (int i=0;i<myvector.size();i++)
std::cout << ' ' << myvector[i];
std::cout << '\n';

Output:
myvector contains: 1 2 3 4 5 100 100 100 0 0 0 0
```

- 向量大小 `vec.size();`
- 清空`vec.clear();`


## 基本用法 ##

```cpp

// constructing vectors
#include <iostream>
#include <vector>

int main ()
{
  // constructors used in the same order as described above:
  std::vector<int> first;                                // empty vector of ints
  std::vector<int> second (4,100);                       // four ints with value 100
  std::vector<int> third (second.begin(),second.end());  // iterating through second
  std::vector<int> fourth (third);                       // a copy of third

  // the iterator constructor can also be used to construct from arrays:
  int myints[] = {16,2,77,29};
  std::vector<int> fifth (myints, myints + sizeof(myints) / sizeof(int) );

  std::cout << "The contents of fifth are:";
  for (std::vector<int>::iterator it = fifth.begin(); it != fifth.end(); ++it)
    std::cout << ' ' << *it;
  std::cout << '\n';

  return 0;
}


The contents of fifth are: 16 2 77 29
```

## 特殊类型 ##

`vector`的元素不仅仅可以使`int`,`double`,`string`,还可以是`结构体`，但是要注意：结构体要定义为全局的，否则会出错。下面是一段简短的程序代码：

```cpp
#include<stdio.h>
#include<algorithm>
#include<vector>
#include<iostream>
using namespace std;

typedef struct rect
{
    int id;
    int length;
    int width;

　　//对于向量元素是结构体的，可在结构体内部定义比较函数，下面按照id,length,width升序排序。
　　bool operator< (const rect &a)  const
    {
        if(id!=a.id)
            return id<a.id;
        else
        {
            if(length!=a.length)
                return length<a.length;
            else
                return width<a.width;
        }
    }
}Rect;

int main()
{
    vector<Rect> vec;
    Rect rect;
    rect.id=1;
    rect.length=2;
    rect.width=3;
    vec.push_back(rect);
    vector<Rect>::iterator it=vec.begin();
    cout<<(*it).id<<' '<<(*it).length<<' '<<(*it).width<<endl;    

return 0;

}
```

## 算法 ##

需要头文件`#include <algorithm>`

- 使用**reverse**将元素翻转 

` reverse(vec.begin(),vec.end()); ` 

- 使用**sort**排序 `sort(vec.begin(),vec.end());`(默认是按升序排列,即从小到大). 可以通过重写排序比较函数按照降序比较，如下： 定义排序比较函数：

```cpp
bool Comp(const int &a,const int &b)
{
    return a>b;
}
//调用时:sort(vec.begin(),vec.end(),Comp)，这样就降序排序。
```

