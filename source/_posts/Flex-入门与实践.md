---
title: Flex入门
date: 2017-03-03 16:19:01
tags: CSS 布局 
---

# 前言
说到CSS，其实最头疼的就是兼容性问题，所以，要用Flex布局，首先也要先知道它的兼容性。戳[这里](http://caniuse.com/#search=flex)可以看到Flex的兼容性其实并不理想，对桌面的支持很不理想，但是，对移动端的支持还是可以的，因此，现在越来越多的移动端开始使用Flex进行布局了。
本文主要记录了学习阮一峰老师关于Flex布局博客的一些心得。
原文戳这里：
- [Flex 布局教程：语法篇](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html)
- [Flex 布局教程：实例篇](http://www.ruanyifeng.com/blog/2015/07/flex-examples.html)

注：开坑一个多月都没有写完之后，写小程序的时候益发觉得flex的重要，还是决定先把这个坑填上---2017.04.25

# Flex使用
使用`display:flex`即可将该容器指定为"flex"布局。
需要注意的是，容器设置为:"flex"布局后，子元素的"float"，"clear","vertical-align"属性将失效。

# 基本概念
- 容器：设置了`display:flex`属性的元素，被称为容器。
- 项目：容器内的子元素被称为项目(item)
- 主轴：水平轴线(main axis)
- 交叉轴：垂直的轴线(cross axis)
- main start: 主轴的开始位置叫main start
- main end: 主轴的结束位置叫main end
- cross start: 交叉轴的开始位置叫做cross start
- cross end: 交叉轴的结束位置叫cross end
- main size: 单个项目占据的主轴空间叫做main size
- cross size: 单个项目占据的交叉轴空间叫做cross size

![flex1](flex1.png)

# 容器的属性
在容器上可以设置以下六个属性：
- flex-direction: 主轴的方向
- flex-wrap: 如何换行
- flex-flow: flex-direction + flex-wrap的简写
- justify-content: 项目在主轴上的对齐方式
- align-items: 项目在交叉轴上的对齐方式
- align-content: 如果有多根轴线的对齐方式，如果项目只有一根轴线，该属性不起作用

## flex-direction
该属性决定了主轴的方向(即项目的排列方向)
再说直接点就是水平排列还是垂直排列。
该属性可能取4个值：
- row (默认值)： 水平从左到右排列
- row-reverse : 水平从右到左排列
- column : 垂直从上到下排列
- column-reverse : 垂直从下到上排列
如下图所示：
分别是：column-reverse,column,row,row-reverse
![flex-direction](flex-direction.png)

## flex-wrap
该属性决定了在主轴上排列不下所有项目时，该如何换行。
该属性可能取3个值：
- nowrap (默认值): 不换行
- wrap :  换行，第一行在上面
- wrap-reverse: 换行，第一行在下方
如下图所示：
nowrap:
![flex-wrap-nowrap](flex-wrap-nowrap.png)
wrap:
![flex-wrap-wrap.jpg](flex-wrap-wrap.jpg)
wrap-reverse:
![flex-wrap-wrap-reverse.jpg](flex-wrap-wrap-reverse.jpg)

从[flex示例](https://github.com/liu-zhuang/flex)中可以看出，nowrap时其实并不会出现滚动条，而是将所有项目等比缩放了。

## flex-flow
该属性是flex-direction和flex-wrap的缩写
默认为: flex-flow: row nowrap;

## justify-content
该属性定义了项目在主轴上的对齐方式。
可取以下几个值：
- flex-start(默认值) : 左对齐
- flex-end : 右对齐
- center : 居中
- space-between: 两端对齐，项目之间的间隔都相等
- space-around: 每个项目的两侧都相等。因此，项目之间的间隔比项目与边框的间隔大一倍。

比较抽象的是最后两个的区别，可以看下图加强理解： 
![justify-content.png](justify-content.png)

## align-item 
该属性定义项目在交叉轴上如何对齐
可取以下几个值：
- flex-start : 交叉轴的起点对齐
- flex-end ： 交叉轴的终点对齐
- center ： 交叉轴的中点对齐
- baseline ： 项目的第一行文字的基线对齐
- stretch（默认值）：如果项目未设置高度或者设为auto，将占满整个容器高度

## align-content
该属性定义了多跟轴线的对齐方式。如果项目只有一根轴线，则该属性不起作用。
可取的值如下：
- flex-start: 交叉轴的起点对齐
- flex-end: 交叉轴的终点对齐
- center: 交叉轴的中点对齐
- space-between: 与交叉轴两端对齐，轴线之间的间隔平均分布
- space-around: 每根轴线两侧的间隔都相等。所以轴线之间的间隔比边框的间隔大一倍
- stretch(默认值): 轴线占满整个交叉轴

这种情况用的比较少，就不多写了

# 项目的属性
以下6个属性设置在项目上： 
- order
- flex-grow
- flex-shrink
- flex-basis
- flex 
- align-self

## order
该属性定义项目的排列顺序，数值越小，排序越靠前，默认为0

## flex-grow
该属性定义项目的放大比例，默认为0，即如果存在剩余空间，也不放大。
如果所有项目的flex-grow均为1，那么所有的项目将均分容器的剩余空间
如果一个项目的flex-grow为2，其他为1的话，为2的这个项目分配到的剩余空间是其他项目的2倍

## flex-shrink
该属性定义了项目的缩写比例，默认为1，记如果容器空间不足，则该项目将缩小
如果所有的项目的flex-shrink均为1，那么所有项目将等比缩小
如果某一个项目的flex-shrink为0，则该项目不会被缩小
注：该属性值负数无效

## flex-basis
该属性定义了在分配多余空间之前，项目占据的主轴空间，默认为auto，即项目本来的大小

## flex
该属性是flex-grow, flex-shrink, flex-basis的简写。默认值为 0 1 auto; 意思是不放大不缩小，原始大小
建议优先写这个值。
比如一个容器里面有两个元素，一个是固定宽度300px，一个是自适应，那么用flex写法就是先将容器定义为`display:flex`，再将固定宽度300px的项目设置属性为`flex:0 1 300px`，自适应的设为`flex: 1 1 auto`

## align-self
该属性允许单个项目有与其他项目不一样的对齐方式，可覆盖align-items属性

演示：[地址](https://github.com/liu-zhuang/flex)

备注： 填坑填不动了，实践单独分一篇写吧。
 