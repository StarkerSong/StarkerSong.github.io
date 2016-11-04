---
layout: post
title: title: "OpenGL(3)-多边形绘图"
date: 2016-11-04 15:44:34 +0800
categories: OpenGL
tags: glPolygonMode glFrontFace
---
* content
{:toc}




多边形是由多条线段首尾相连而形成的闭合区域。OpenGL规定，一个多边形必须是一个“凸多边形”。通过点、直线和多边形，就可以组合成各种几何图形。一段弧可以看成是是很多短的直线段相连，这些直线段足够短，以至于其长度小于一个像素的宽度。通过位于不同平面的相连的小多边形，还可以组成一个“曲面”。


## 绘制多边形 ##

### 1.多边形的两面以及绘制方式 ###

从三维的角度来看，一个多边形具有两个面。每一个面都可以设置不同的绘制方式：

- 填充（默认方式）
- 只绘制边缘轮廓线
- 只绘制顶点

可以为两个面分别设置不同的显示方式：

```cpp
void glPolygonMode(GLenum face,GLenum mode)
```

**face参数**确定显示模式将适用于物体的哪些部分，控制多边形的正面和背面的绘图模式：

1. GL_FRONT 表示显示模式将适用于物体的前向面（也就是物体能看到的面）
2. GL_BACK  表示显示模式将适用于物体的后向面（也就是物体上不能看到的面）
3. GL_FRONT_AND_BACK  表示显示模式将适用于物体的所有面

**mode参数**确定选中的物体的面以何种方式显示（显示模式）：

1. GL_POINT 表示只显示顶点，多边形用点显示
2. GL_LINE  表示显示线段，多边形用轮廓显示
3. GL_FILL  表示显示面，多边形采用填充形式


```cpp
void glPolygonMode(GLenum face,GLenum mode) //为两个面设置不同的显示方式

glPolygonMode(GL_FRONT, GL_FILL);           // 设置正面为填充方式
glPolygonMode(GL_BACK, GL_LINE);            // 设置反面为边缘绘制方式
glPolygonMode(GL_FRONT_AND_BACK, GL_POINT); // 设置两面均为顶点绘制方式
```

### 2.反转(切换正反面) ###

一般约定为“顶点以逆时针顺序出现在屏幕上的面”为“正面”，另一个面即成为“反面”。但也有一些表面比较特殊。例如“麦比乌斯带”，可以全部使用“正面”或全部使用“背面”来表示。

```cpp
void glFrontFace(GLenum mode)//控制多边形的正面是如何决定的
```

**mode参数**的值:

1. `GL_CCW` 表示窗口坐标上投影多边形的顶点顺序为逆时针方向的表面为正面。(CounterClockWise,默认情况)
2. `GL_CW`  表示顶点顺序为顺时针方向的表面为正面。(ClockWise)
顶点的方向又称为环绕。

```cpp
glFrontFace(GL_CCW);  // 设置CCW方向为“正面”，CCW即CounterClockWise，逆时针
glFrontFace(GL_CW);   // 设置CW方向为“反面”，CW即ClockWise，顺时针
```

### 3.剔除多边形表面 ###

在一个全部由不透明封闭表面组成的场景中，背面多边形是永远看不见的。剔除这些不可见的多边形对于加速图形的渲染有很大的益处。

`OpenGL`中使用`glCullFace()`来进行剔除操作:

```cpp
//剔除操作的步骤：

1.glEnable(GL_CULL_FACE)：启动剔除功能；(glDisable(GL_CULL_FACE)关闭剔除功能)
2.使用void glCullFace(GLenum mode)指明前面或后面的多边形是否要剔除
    mode参数的值:
	1.GL_FRONT   表示剔除正面
	2.GL_BACK     剔除反面
	3.GL_FRONT_AND_BACK  剔除正反两面的多边形

```

### 4.镂空多边形 ###

直线可以被画成虚线，而多边形则可以进行镂空。

`OpenGL`中使用`glPolygonStipple()`函数设置镂空的样式:


```cpp
镂空操作的步骤:
1.使用glEnable(GL_POLYGON_STIPPLE)来启动镂空模式。（使用glDisable(GL_POLYGON_STIPPLE)可以关闭之）。
2.使用glPolygonStipple()来设置镂空的样式。
```





