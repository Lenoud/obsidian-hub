# Python turtle 绘图库

turtle（海龟）库是 Python 标准库之一，主要用于程序设计入门的图形绘制。

## turtle 库概述

turtle（海龟）库是turtle绘图体系python的实现；

turtle绘图体系：1969年诞生，主要用于程序设计入门；

turtle库是python的标准库之一；属于入门级的图形绘制函数库；

説名：python计算生态=标准库+第三方库

标准库：是随解释器直接安装到操作系统中的功能模块；

第三方库：需要经过安装才能使用的功能模块；

库：library、包package、模块module统称为模块；

turtle库绘制原理：有一只海龟在窗体正中心，在画布上游走，走过的轨迹形成了绘制的图形，海龟由程序控制，可以自由改变颜色、方向宽度等；

二、turtle绘图窗体：

```python
turtle.setup(width,height,startx,starty)
#setup设置窗体大小，四个参数中后两个参数非必选参数；
#setup()是非必须的；
```

turtle的空间坐标体系：

![[1689497550950-d1598fdc-73b8-41de-9bc7-e9ed7ea63c92.jpeg]]

turtle的移动：

turtle.goto(x,y)

![[1689497550980-e29243f0-2bcc-4d6c-a058-3a866c708836.jpeg]]

```text
import turtle
turtle.goto(100,100)
turtle.goto(100,-100)
turtle.goto(-100,-100)
turtle.goto(-100,100)
turtle.goto(0,0)
```

![[1689497550985-076ce824-a570-47bc-9e59-3469c8294f2d.png]]

```text
#画圆的用法
turtle.circle(r,angle)
#当前距离后退
turtle.bk(d)
#当前距离前进
turtle.fd(d)
```

turtle角度坐标体系：

![[1689497550964-9d20ff89-4a80-411f-bd25-2bd175fcb2cd.png]]

```text
turtle.seth(angle)
#seth()改变海龟行进方向；
#angle为据对度数；
#seth()只改变呢方向但是不行进；
```

同时turtle还提供了left和right方法：

turtle.right(angle)

turtle.left(angle)

```text
import turtle
turtle.left(45)
turtle.fd(150)
turtle.right(135)
turtle.fd(300)
turtle.left(135)
turtle.fd(150)
```

turtle同时兼容使用RGB色彩体系：

1、常用的RGB色彩体系如下：

![[1689497551289-53135a1d-7bd7-4b84-a5cd-232e81e467f5.png]]

![[1689497551752-372ba855-8ee2-46c4-ab62-bbecdd090be6.png]]

使用RGB色彩模式写法为：

turtle.colormode(mode)

支持RGB的小数模式和整数模式；

三、turtle画笔控制函数：

turtle.penup():表示抬起画笔，海龟在飞行；可以简写成turtle.pu()

turtle.pendown():表示画笔落下，海龟在爬行；可以简写成turtle.pd()

turttle.pensize(width):表示画笔的宽度，也可以使用turtle.width(width)

turtle.pencolor(color):color为颜色字符串或者 RGB值；

turtle.forward(d):向前行进距离；可以简写为turtle.fd(d)，d为整数可以为负数；

turtle.circle(r,extent=NONE):根据半径r绘制extent角度的弧形，r默认在圆心左侧R距离的位置；extent:绘制角度默认360度是整圆；

下边是python简单绘制代码：

```text
#PythonDraw.py
import turtle as tu
tu.setup(650,350,200,200)
tu.penup()
tu.fd(-250)
tu.pendown()
tu.pensize(25)
tu.seth(-40)
for i in range(4):
    tu.pencolor("yellow")
    tu.circle(40,80)
    tu.pencolor("gold")
    tu.circle(-40,80)
tu.circle(40,80/2)
tu.fd(40)
tu.circle(32,180)
tu.fd(40*2/3)
tu.done()
```

笔记是学习北京理工大学[嵩天](https://link.zhihu.com/?target=https%3A//www.icourse163.org/u/songtian425)教授课程笔记；只作为笔记用途；
