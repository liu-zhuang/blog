---
title: CSS3小结
date: 2016-10-14 23:06:27
tags: CSS CSS3
---
# 前言
什么是CSS3？这个问题和什么是HTML5一样，经常让人崩溃，常听到有人说用什么HTML5技术，CSS3技术。。。
其实就只是版本号而已。。。
先上一个小日本在几年前用纯CSS做的哆啦A梦镇楼。。[doraemon](https://github.com/nicky-lau/CSS3/tree/master/doraemon)

# 边框
包括以下点：
- 圆角边框
- 阴影
- 边框图片

## 圆角边框
border-radius是向元素增加圆角边框用的。
语法：border-radius: 左上边框半径 右上边框半径 右下边框半径 坐下边框半径
除了可以为元素增加圆角边框外，还可以画圆，半圆
方法见下方

```HTML
<style>
	/*圆角边框*/
	div.border-radius{
		height:100px;
		width:200px;
		border-radius:10px 20px 30px 40px;/*左上 右上 右下 左下 从左上开始顺时针的顺序*/
		background-color: red;
	}
	/*怎么画圆*/
		/*长和宽一样
		border-radius等于长/宽的一半*/
		div.circle{
			height:100px;
			width:100px;
			border-radius:50px;
			background-color: red;
		}
		/*上半实心圆*/
		/*高度为宽度的一半，同时只设置左上，右上的border-radius宽度，左下和右下均为0*/
		div.top-half-circle{
			height:50px;
			width:100px;
			border-radius:50px 50px 0 0 ;
			background-color: red;
		}
		/*下半实心圆*/
		/*高度为宽度的一半，同时只设置左下，右下的border-radius宽度，左上和右上均为0*/
		div.bottom-half-circle{
			height:50px;
			width:100px;
			border-radius:0 0 50px 50px;
			background-color: red;
		}
	</style>
	<html>
	<!-- 圆形边框 -->
	<div class="border-radius"></div>
	<!-- 圆 -->
	<div class="circle"></div>
	<br>
	<!-- 上半实心圆 -->
	<div class="top-half-circle"></div>
	<br>
	<!-- 下半实心圆 -->
	<div class="bottom-half-circle"></div>
	</html>
	```
## 阴影
box-shadow是向盒子模型添加阴影，支持一个或多个。
语法：box-shadow: X轴偏移量 Y轴偏移量 [阴影模糊半径] [阴影扩展半径] [阴影颜色] [投影方式];
其中投影方式如果不写则是外投影，如果需要内投影则为:inset
如果有多个阴影（一个内，一个外），则使用逗号分隔
x轴偏移量，y轴偏移量的值均可为负数，表示向左和向上阴影
实例：
```HTML
<style>
div.shadow{
	box-shadow:4px 2px 6px #f00, -4px -2px 6px #000, 0px 0px 12px 5px #33CC00 inset;
	height: 100px;
	width:200px;
	border:solid 1px black;
}
</style>
<div class="shadow">
```
## 边框图片
border-image可以为边框设置图片
语法：border-image:url(xx.jpg) 70 70 70 70 repeat/round/stretch 
需要注意的是，除了设置border-image之外，还需要设置border
效果就是把border-image这张图片放大到和块状元素一样的大小，作为border。
``` HTML
<style>
div.border-image{
	height:300px;
	width:300px;
	border:solid 20px;
	border-image:url(border-image.jpg) 20 repeat;
	background-color: red;
}
</style>
<div class="border-image">	
</div>
```
圆角边框告一段路：[源码](https://github.com/nicky-lau/CSS3/tree/master/border-radius)

# 颜色相关
包括以下几点：
- RGBA
- 渐变色彩

## RGBA
RGB是一种色彩标准，由R(red)G(green)B(blue)的变化以及相互叠加而获取各种各样的颜色。
CSS3中增加了A即alpha透明度的参数。
语法：rgba(R,G,B,A)
eg:color:rgba(100,120,60,0.5);

```html
<style>
div.bg-rgba{
	width:100px;
	height:200px;
	border:solid 1px red;
	background-color: rgba(100,120,60,0.5);
}
</style>
<div class="bg-rgba"></div>
```

## 渐变色彩
CSS3中渐变分为线性(linear)和径向(radial)渐变。
语法：linear-gradient(渐变方向，渐变颜色);
其中渐变颜色可以有多个，从起点到终点依次排列
渐变方向可以用角度或者用英文来描述，角度不太直观，用英文比较好记
eg:
to top      : 从下到上
to bottom   : 从上到下
to right    : 从左到右
to top right: 从左下到右上

```html
div.linear{
	width:200px;
	height: 100px;
	background-image:linear-gradient(to right,red,orange,yellow,green,blue,indigo,violet );
}
<div class="linear"></div>
```
CSS3中颜色告一段落：[源码](https://github.com/nicky-lau/CSS3/tree/master/Color)

# 文字与字体
包括以下几点：
- text-overflow与word-warp
- 嵌入字体@font-face
- 文本阴影text-shadow

## text-overflow与word-warp
text-overflow用来设置是否使用一个省略标识(...)来表示对象文本内的溢出。
语法：text-overflow:clip | ellipsis 
clip表示剪切 而ellipsis则表示以省略号代替
但是，仅仅设置text-overflow:ellipsis还是不能实现将溢出部分用省略号代替了，通常还需要设置
white-space:nowrap和overflow:hidden
```HTML
<style>
	.t-o{
		width:100px;
		/*text-overflow: ellipsis;*/
		text-overflow: clip;/*表示剪切*/
		white-space:nowrap;
		overflow: hidden;
	}
</style>
<div class="t-o">
	我是一段比较长的文字
</div>
```

## @font-face
@font-face可以使浏览器加载服务器端端字体，从而直接使用服务器端的字体文件
语法：@font-face { font-family:字体名称; src:url(相对或者绝对路径) }

## text-shadow
text-shadow可以设置文本的阴影效果
语法：text-shadow:X-offset Y-offset Blur Color;
X-Offset：表示阴影的水平偏移距离，其值为正值时阴影向右偏移，反之向左偏移；      
Y-Offset：是指阴影的垂直偏移距离，如果其值是正值时，阴影向下偏移，反之向上偏移；
Blur：是指阴影的模糊程度，其值不能是负值，如果值越大，阴影越模糊，反之阴影越清晰，如果不需要阴影模糊可以将Blur值设置为0；
Color：是指阴影的颜色，其可以使用rgba色。
```HTML
<style type="text/css">
	.t-s{
		font:normal 25px/50px "微软雅黑"; /*font-size: 25px;行高50px*/
		width:300px;
		height:50px;
		text-shadow:1px 1px 1px black;
	}
</style>
<div class="t-s">这是一段有阴影的文字</div>
```

# 背景相关
包括以下几点：
- background-origin
- background-clip
- background-size
- background multiple
- 导航菜单综合使用

## background-origin
background-origin可以设置元素背景图片的起始位置
语法：background-origin:border-box | padding-box | content-box
参数分别表示从边框、内边距、或者内容区域开始显示背景图片
需要注意的是，背景图片必须设置为no-repeat。否则背景图片总是从border-box开始显示

## background-clip
background-clip可以将背景图片做适当的剪裁以适应实际需要
语法：background-clip:border-box | padding-box | content-box | no-clip
同background-origin一样，四个参数分别代表从边框、内边距、内容区域剪裁或者不剪裁

## background-size
background-size可以设置背景图片的大小，以长度或者百分比来设置。还可以通过cover或者contain来对图片进行伸缩。
语法：background-size:auto | 长度值 | 百分比 | cover | contain
参数说明：
auto：默认值，即不改变图片的大小
长度值：可以是width height成对出现，当只写一个值的时候，将其作为图片的width成比例缩放
百分比：0-100%之间的任意值 可以成对出现，也可以只出现一个 规则同长度值
cover:顾名思义为覆盖，使用图片等比例填满容器
contain:将图片等比缩放至某一边紧贴容器为止

## background multiple
多重背景，就是把所有属性都写在background中
```css
background:url() left top/100px 20px no-repeat;// 图片地址 位置 size repeat
```
以上缩写也可以用以下方式代替：
``` CSS
background-repeat : repeat1,repeat2,...,repeatN;
backround-position : position1,position2,...,positionN;
background-size : size1,size2,...,sizeN;
background-attachment : attachment1,attachment2,...,attachmentN;
background-clip : clip1,clip2,...,clipN;
background-origin : origin1,origin2,...,originN;
background-color : color;
<!-- 例如 -->
background-image: url(xxx.jpg),
                   url(xxx.jpg),
                   url(xxx.jpg);
 background-position: left top, 100px 0, 200px 0;
 background-repeat: no-repeat, no-repeat, no-repeat;
```
## 导航菜单综合使用
前面的综合练习,直接上源码:[源码](https://github.com/nicky-lau/CSS3/tree/master/NavMenu)

# CSS3选择器
包括以下几点：
- 属性选择器
- 结构性伪类选择器
- nth-of-type选择器
- enabled选择器
- before和after选择器
- 切换背景图像综合实践

## 属性选择器




