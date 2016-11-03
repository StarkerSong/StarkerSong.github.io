---
layout: post
title: "OpenGL(2)-点、线、点画线、多边形"
date: 2016-11-03 20:28:45 +0800
categories: OpenGL
tags: GL_MODELVIEW  GL_PROJECTION gluPerspective glViewport glOrtho glFrustum
---
* content
{:toc}

![](http://i.imgur.com/eF2NgIS.jpg)








## 基础知识 ##

1. **渲染：**对一个三维物体进行几何描述，并且把它转换为屏幕上的一幅图像，这个过程就叫渲染。
2. **纹理贴图：**通过一副图像向一个多边形提供额外细节的技巧称为纹理贴图。我们所提供的图像称为纹理，纹理中每个单独的元素称为纹理单元（或纹理像素，texel）。
3. **过滤（filtering）：**在一个物体的表面上拉伸或压缩纹理单元（纹理像素）的过程称为过滤。
4. **混合（blending）：**指屏幕上颜色或物体的组合。混合可以用于多种目的，如制作透明效果、反射效果等。
5. **裁剪区域：**窗口（即屏幕）是以像素为单位进行度量的。裁剪区域指的是占据窗口的笛卡尔坐标空间中的区域。也可以解释为填充窗口的笛卡尔坐标空间中的区域。注意，裁剪区域使用的是笛卡尔坐标系统。
6. **视口：**因为裁剪区域的宽度和高度很少正好与窗口的宽度和高度（以像素为单位）相匹配，所以需要把坐标系统从逻辑笛卡尔坐标空间映射到物理屏幕像素坐标空间。视口就是窗口中用于绘制裁剪区域的客户区域。这里，要注意窗口与视口的区别，视口在窗口中指定，我们可以使用视口来缩小或者放大窗口中的图像。//类似4所说的东西
7. **管线：**它用于描述一种过程，该过程可能涉及两个或更多个独特的阶段或步骤。
8. **OpenGL命令缓冲区：**当应用程序进行OpenGL API函数调用时，这些命令被放置在一个命令缓冲区中。最终，这个缓冲区中会填满API 调用命令、顶点数据、纹理数据之类的东西。当缓冲区被刷新时，命令和数据就会被传递给管线的下一个阶段。
9. **变换和光照（T&L）：**这是一个数学计算密集型的阶段。在变换阶段，描述物体几何形状的顶点被重新计算（经历多次不同坐标空间的变换），以确定这个物体的位置和朝向。同时进行的光照计算阶段（世界坐标空间中）将确定每个顶点该具有的颜色和亮度。
10. **光栅化：**该阶段根据几何图形、颜色和纹理数据实际创建彩色图像。然后，图像被放入到帧缓冲区之中。
11. **帧缓冲区：**即图形显示设备的内存。图像放入到帧缓冲区意味着将会在屏幕上显示（刷新帧缓冲区时）。

## 相关知识 ##

**双缓冲技术:** 

`glutSwapBuffers`函数是`OpenGL`中`GLUT`工具包中用于实现双缓冲技术的一个重要函数。该函数的功能是交换两个缓冲区指针。当我们进行复杂的绘图操作时，画面便可能有明显的闪烁。解决这个问题的关键在于使绘制的东西同时出现在屏幕上。

双缓冲技术，是指使用两个缓冲区: 前台缓冲和后台缓冲。前台缓冲即我们看到的屏幕，后台缓冲则在内存当中，对我们来说是不可见的。每次的所有绘图操作都在后台缓冲中进行，当绘制完成时， 把绘制的最终结果复制到屏幕上，这样，我们看到所有`GDI`元素同时出现在屏幕上，从而解决了频繁刷新导致的画面闪烁问题。

在`OpenGL`中实现双缓冲技术的一种简单方法:

```cpp
1.在调用glutInitDisplayMode函数时， 开启GLUT_DOUBLE，即glutInitDisplayMode(GLUT_RGB | GLUT_DOUBLE);。这里将我们惯用的GLUT_SINGLE替换为GLUT_DOUBLE，意为要使用双缓冲而非单缓冲。
2.调用glutDisplayFunc(display)注册回调函数时， 在回调函数中所有绘制操作完成后调用glutSwapBuffers()交换两个缓冲区指针。 
3.调用glutIdleFunc注册一个空闲时绘制操作函数， 注册的这个函数再调用display函数。
```


## 函数介绍 ##


### glMatrixMode ###

```cpp
void glMatrixMode(GLenum mode)

GL_MODELVIEW： 模型视景矩阵堆栈
GL_PROJECTION：投影矩阵堆栈 
GL_TEXTURE ：  纹理矩阵堆栈

```

由于对不同的矩阵有不同的操作，在设置当前的矩阵后，接下来所有调用`OpenGL`库函数的功能都必须确定是针对当前矩阵的。

```cpp
glMatrixMode（GL_MODELVIEW ）；//设置当前矩阵为模型视景矩阵
gluPerspective(45.0f,(GLfloat)cx/(GLfloat)cy,0.1f,100.0f)；//对图像进行透视投影，以将三维物体显示在二维平面上
```

上述操作是错误的，由于设置当前矩阵为视景矩阵，计算机会把该矩阵当做投影矩阵，与`gluPerspective()`指定的矩阵进行乘法运算，最终导致错误，没有显示图像。


### glLoadIdentity() ###

`glLoadIdentity()`恢复初始坐标系，类似复位操作，用`4X4`的单位矩阵替换当前矩阵。当调用`glLoadIdentity（）`后，实际上是将当前点移动到屏幕坐标中心。



### glOrthon ###


创建平行视景体，即使距离观察者很远的对象看起来不会变小。实际上该函数的操作是创建正射投影矩阵，并且用这个矩阵乘以当前矩阵。

```cpp
glOrtho(GLdouble left, GLdouble right, GLdouble bottom, GLdouble top, GLdouble near, GLdouble far)
```

near和far值同时为正或同时负，值不能相同。如果没有其他变换 ，正射投影的方向平行于Z轴，且视点朝向Z负轴。far和near都为负值时，物体在视点前面，都为正值时在视点后面。如果near和far值相同，视景体没有深度被压缩成平面就会显示不正确。

### glPushMatrix()、glPopMatrix() ###

相当于栈里的入栈和出栈。`glPushMatrix`其实就是把当前状态做一个副本放入堆栈之中；`glPopMatrix()`从栈中取出一个副本，恢复成副本的状态。

### glFlush和glutSwapBuffers ###

`glFlush`是强制马上输出命令执行的结果，而不是存储在缓冲区中，继续等待其他OpenGL命令。当执行双缓冲交换的时候，使用`glutSwapBuffers`。但是在有`glutSwapBuffers`的情况下，不需要`glFlush`就可以达到同样的效果，因为我们执行双缓冲交换的时候，就隐形的执行了一次刷新操作。



### glBegin和glEnd ###


绘制图像的开始和结束标志。在他们之间可以

**执行的函数：**

```cpp
glVertex*()    设置顶点坐标
glColor*()     设置当前颜色
glIndex*()     设置当前颜色表
glNormal*()    设置法向坐标
glCoord*()     产生坐标
glCallList(),glCallLists()    执行显示列表
glTexCoord*()  设置纹理坐标
glEdgeFlag*()  控制边界绘制
glMaterial*()  设置材质
```

**绘制图形的类型：**

```cpp
GL_POINTS         单个顶点集
GL_LINES          多组双顶点线段
GL_POLYGON        单个简单填充凸多边形
GL_TRAINGLES      多组独立填充三角形
GL_QUADS          多组独立填充四边形
GL_LINE_STRIP     不闭合折线
GL_LINE_LOOP      闭合折线
GL_TRAINGLE_STRIP 线型连续填充三角形串
GL_TRAINGLE_FAN   扇形连续填充三角形串
GL_QUAD_STRIP     连续填充四边形串
```


![](http://i.imgur.com/JHqU9Q6.png)



**点**

```cpp
void glPointSize(GLfloat size)  // 设置被渲染点的宽度
```

**线**

```cpp
void glLineWidth(GLfloat width)    // 用于直线的渲染

GL_LINES ：指定两个顶点，在它们之间绘制一条直线。如果为GL_LINES指定了奇数个顶点，那么最后一个顶点会被忽略。
GL_LINE_STRIP ：线带，它允许指定一个顶点列表，并绘制一条经过所有这些顶点的连续的线。
GL_LINE_LOOP：线环，它与线带非常类似，会在顶点列表的最后一个顶点和第一个顶点之间也绘制一条直线。
```

**点画线**


1. 使用`glEnable(GL_LINE_STIPPLE)`;来启动虚线模式（使用`glDisable(GL_LINE_STIPPLE)`可以关闭之）。
2. 然后，使用`glLineStipple`来设置虚线的样式。

```cpp
void glLineStipple(GLint factor, GLushort pattern);       //     void glLineStipple(1, 0xf00f); 
注：pattern是由1和0组成的长度为16的序列，从最低位开始看。
	如果为1，则直线上接下来应该画的factor个点将被画为实的；
	如果为0，则直线上接下来应该画的factor个点将被画为虚的。
```



**多边形**

1.多边形的两面以及绘制方式

从三维的角度来看，一个多边形具有两个面。每一个面都可以设置不同的绘制方式：填充、只绘制边缘轮廓线、只绘制顶点，其中“填充”是默认的方式。可以为两个面分别设置不同的方式。

```cpp
glPolygonMode(GL_FRONT, GL_FILL);           // 设置正面为填充方式
glPolygonMode(GL_BACK, GL_LINE);            // 设置反面为边缘绘制方式
glPolygonMode(GL_FRONT_AND_BACK, GL_POINT); // 设置两面均为顶点绘制方式
```

2.反转

一般约定为“顶点以逆时针顺序出现在屏幕上的面”为“正面”，另一个面即成为“反面”。但也有一些表面比较特殊。例如“麦比乌斯带”，可以全部使用“正面”或全部使用“背面”来表示。

```cpp
glFrontFace(GL_CCW);  // 设置CCW方向为“正面”，CCW即CounterClockWise，逆时针
glFrontFace(GL_CW);   // 设置CW方向为“反面”，CW即ClockWise，顺时针
```

## 坐标系 ##

1. Object or model coordinates
2. World coordinates
3. Eye (or Camera) coordinates
4. Clip coordinates
5. Normalized device coordinates
6. Window (or screen) coordinates　

世界坐标系以屏幕中心为原点(0, 0, 0)。你面对屏幕，你的右边是x正轴，上面是y正轴，屏幕指向你的为z正轴。长度单位这样来定： 窗口范围按此单位恰好是(-1,-1)到(1,1)。
当前绘图坐标系是绘制物体时的坐标系。
程序刚初始化时，世界坐标系和当前绘图坐标系是重合的。当用glTranslatef()，glScalef(), glRotatef()对当前绘图坐标系进行平移、伸缩、旋转变换之后， 世界坐标系和当前绘图坐标系不再重合。改变以后，再用glVertex3f()等绘图函数绘图时，都是在当前绘图坐标系进行绘图，所有的函数参数也都是相 对当前绘图坐标系来讲的。


## 参考资料 ##

- 《OpenGL编程指南》
- [点、边和图形（一）点](http://www.cnblogs.com/MenAngel/p/5633146.html)















