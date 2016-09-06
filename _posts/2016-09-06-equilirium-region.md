---
layout: post
title: "块体理论平衡区域图三维可视化"
date: 2016-09-06 15:15:47 +0800
categories: equilirium-region
tags: block-theory equilirium-region OpenGL
---
* content
{:toc}


## 问题描述 ##

如下图1所示区域为平衡区域，粉红色填充区域为节理锥在平面上的投影。在图2中节理锥粉红色填充区域与全空间赤平投影中的三维锥形节理锥相关联，由于平衡区域投影与赤平投影在空间中投影方法的一致性，根据赤平投影三维可视化原理和方法绘制出平衡区域图的三维图形。

![](http://i.imgur.com/YiQVUiQ.jpg)











绘制平衡区域图的三维构造与Pyramid.cpp文件中下面的函数方法是相同的：

```cpp
void Pyramid::renderInSphere(PyramidEdgeArray *ea, PlaneArray *pa /* = 0 */)
```

![](http://i.imgur.com/6D9gldF.jpg)



## 节理锥侧面 ##

在上述的图3中，绘制**节理锥侧面**的核心代码部分为：基本原理是将凸面切割成三角形，最后再进行绘制

![](http://i.imgur.com/Bucfnib.jpg)

```cpp
  glBegin (GL_TRIANGLES );
  for ( int i = 0 ; i < _triangles. size (); i += 3)
  {
	  glNormal3v (_vertices [ _triangles[ i ]]);
	  glVertex3v (_vertices [ _triangles[ i ]]);
	  glNormal3v (_vertices [ _triangles[ i + 1]]);
	  glVertex3v (_vertices [ _triangles[ i + 1]]);
	  glNormal3v (_vertices [ _triangles[ i + 2]]);
	  glVertex3v (_vertices [ _triangles[ i + 2]]);
  }
  glEnd ();

```

## 节理锥凸面 ##


![](http://i.imgur.com/FOTri2u.jpg)


```cpp
  glBegin (GL_POLYGON );
  glNormal3v (n );
  for ( int j = 0 ; j < curpoly. size (); ++ j) 
  {
	  //int vi = curpoly[j];
	  //vector4 const &v = _vertices[curpoly[j]];
	  glVertex3v (_vertices [ curpoly[ j ]]);
  }
  glEnd ();

```

## 主窗口文件 ##

添加结构面信息：双面滑动的向量方向，滑动角度，相交的法线向量的编号

```cpp
  // 双面滑动
  for ( int i = 0; i< jpPlaneArray ->itemCount ()- 1; ++i ) 
  {
             vector4 const &n1 = jpPlaneArray ->itemAt ( i). normal ();//结构面i的法向量
             for ( int j = i+ 1 ; j <jpPlaneArray -> itemCount(); ++j ) 
             {
                 vector4 const &n2 = jpPlaneArray ->itemAt ( j). normal ();//结构面j的法向量
                 vector4 sd = normalize_cross ( n1, n2); //双面滑动w1 X w2
                 //sd.Normalize();

                 real_type dotval = sd *rfdir ;
                 if ( dotval >0 )
                         slidingdirs . append( PyramidEdge (sd , acos ( dotval), i, j));
                 else
                         slidingdirs . append( PyramidEdge (-sd , acos (- dotval), i, j));

                 jpEdgeArray ->addItem ( PyramidEdge( sd , acos (dotval ), i , j ));
                 jpEdgeArray ->addItem ( PyramidEdge(- sd , acos (-dotval ), j , i ));
             }
  }

  jpi ->render ( _jp_edge_array .data (), _jp_plane_array . data());      // 绘制节理锥
```

## 总结 ##

其实绘制平衡区域图的方法与绘制赤平投影的方法是一致的，只要将绘制平衡区域的二维按照赤平投影的方法转换到三维空间就可以了。具体的转换的难点就在于如何获取各个区域的绘制的点的信息了。涉及知识点：

- 空间几何
- 平衡区域图