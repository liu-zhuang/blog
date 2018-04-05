---
title: LESS 小记
date: 2016-10-10 20:30:27
tags: LESS CSS 
---

# 前言
几年前第一次在[BootStrap中文网](http://www.bootcss.com)上看到[LESS](http://www.bootcss.com/p/lesscss/)的时候，真的有种梦想成真的感觉，一直希望可以像写编程语言一样写CSS的愿望终于实现了。
当时随便学了学之后发现现实工作中不大有机会用到（毕竟首先不是专业做前端/美工，其次做的内部系统对css的要求其实没那么高），也就基本荒废了（只记得基本的语法了）
正好最近一个项目，有需要稍微修改一下[font awesome](http://fontawesome.io)的样式，就把font awesome的LESS下载下来研究了一下，发现还是有点意思的，于是重新温故而知新一下。

# 开发工具
个人感觉最好用的是企鹅公司的一个大牛开发的名为[koala](http://koala-app.com)的桌面app，使用简单方便。不过不更新很久了-。-
作者的微博和blog也停更很久了=v=.使用起来很简单，因为一共也没有几个按钮和菜单，同时是多语言版本的，切换到简体中文，重启app即可。

# 语法介绍
下面开始正文-。-

## 注释
- /**/
- //
less有以上两种注释方式，第一种在编译为css之后会保留，第二种就是真的注释了，在编译为css之后不会保留
ps：这个特指编译出来的是普通版本，如果选择编译出压缩版本，则无论哪种方式书写的注释都不会出现在css文件中

## 变量
LESS中使用@作为变量的关键字,":"作为赋值表达式,例如:
``` LESS
@myWidth:300px; // 这里声明了@myWidth这个变量，并且赋值为300px

div{
width:@myWidth; // 直接使用这个变量
height: @myWidth;
border:solid 1px red;
}

// 输出为：(compress)
div{width:300px;height:300px;border:solid 1px #f00}
```

## 混合
在LESS中可以id和class选择器的样式拿来复用。

### 混合的基本用法

例如：
``` LESS
@myWidth:300px;

.div1{
width:@myWidth;
height: @myWidth;
border:solid 1px red;
}

.newDiv{
.div1; // 在这里可以通过混合来复用.div1的样式
background-color:blue;
}
```
但是有几点要注意：
- 编译器并不会override
- 不支持html标签标记的复用
eg:
``` LESS
@myWidth:300px;

.div1{
width:@myWidth;
height: @myWidth;
border:solid 1px red;
}

.newDiv{
.div1;
width:500px;
background-color:blue;
}
```
上面这种写法，希望是可以将width变为500px,但是实际效果是:
``` CSS
.div1 {
width: 300px;
height: 300px;
border: solid 1px red;
}
.newDiv {
width: 300px;
height: 300px;
border: solid 1px red;
width: 500px;
background-color: blue;
}
```
也就是说LESS只会简单的混合。
另外,如果尝试混合html标签标识的样式会报错：“ParseError: Unrecognised input”。eg:
```LESS
@myWidth:300px;

div{
width:@myWidth;
height: @myWidth;
border:solid 1px red;
}

.newDiv{
div;
width:500px;
background-color:blue;
}
```
### 带参数的混合
在混合的时候，还可以带上参数：
``` LESS
.border(@color){
border:solid @color 2px;
}

.border_y{
.border(yellow);
}
//输出
.border_y {
border: solid #ffff00 2px;
}

```
同时，也可以为参数带上默认值：
``` LESS
.border(@color:red){
border:solid @color 2px;
}

.border_y{
.border;
}
// 输出：
.border_y {
border: solid #ff0000 2px;
}

```
带参数的混合有以下几点需要注意以下：
- 带参数的样式是不会编译进css文件的（即使设置了默认值参数值也不行）
- 为了和不带参数的样式区分开，最好在混合带参数的样式时，在后面加上括号--即使使用默认值的情况下也这么做。

### 混合的用途
目前想到比较好的场景是在写CSS的兼容性的时候:
``` LESS
.bor-radius(@rWidth){
-webkit-border-radius: @rWidth;
-moz-border-radius: @rWidth;
border-radius: @rWidth;
}

.r3{
.bor-radius(3px);
}

// 输出:
.r3 {
-webkit-border-radius: 3px;
-moz-border-radius: 3px;
border-radius: 3px;
}

```

## 匹配模式
匹配模式有点类似于C#里面枚举的感觉？以css画三角来说明：
```HTML
.triangle(top,@w:5px,@color:#ccc){
border-width:@w;
border-color:transparent transparent @color transparent;
border-style: dashed dashed solid dashed;
}

.triangle(right,@w:5px,@color:#ccc){
border-width:@w;
border-color: transparent transparent transparent @color;
border-style:dashed dashed dashed solid ;
}

.triangle(bottom,@w:5px,@color:#ccc){
border-width:@w;
border-color:@color transparent transparent transparent;
border-style:solid dashed dashed dashed;
}

.triangle(left,@w:5px,@color:#ccc){
border-width:@w;
border-color: transparent @color transparent transparent;
border-style:dashed solid  dashed dashed;
}

.triangle(@_,@w:5px,@color:#ccc){
width:0;
height: 0;
overflow: hidden;
}

.sj{
.triangle(left);
}

// 输出：
.sj {
border-width: 5px;
border-color: transparent #cccccc transparent transparent;
border-style: dashed solid  dashed dashed;
width: 0;
height: 0;
overflow: hidden;
}

```
可以看到上面有.triangle(top...),.triangle(left...),.triangle(right...),.triangle(bottom...)表示三角尖的四个方向，同时还有一个.triangle(@_,...),这个表示不管用的是.triangle的哪一种，都要要加上这个样式。
因此在.sj中，括号中写哪个方向，就会用哪个方向的样式，同时加上.triangle(@_)这个class的样式
## 运算
在LESS中可以对变量进行"+ - * /"运算，例如：
```HTML
@my:20%;
@my2:200px;

.d1{
width:@my/2;
height: @my2+20;
}
// 输出:
.d1 {
width: 10%;
height: 220px;
}

```
## 嵌套规则
LESS还有个很厉害的地方就是可以像写html代码一样嵌套：
```html
<ul>
	<li>
		<a></a>
	</li>
</ul>
```
可以看到，html的嵌套就是这样ul里面嵌套li,li里面再嵌套a标签
那么LESS的嵌套呢？eg:
```LESS
.list{
	width:200px;
	margin:0 auto;
	padding:0;
	list-style: none;
	li{
		height: 30px;
		line-height: 30px;
		background-color: pink;
		margin-bottom: 5px;
	}
	a{
		float: left;
	}
	span{
		float: right;
	}
}
// 可以看到，li,a,span嵌套在.list里面，输出如下：
.list {
  width: 200px;
  margin: 0 auto;
  padding: 0;
  list-style: none;
}
.list li {
  height: 30px;
  line-height: 30px;
  background-color: pink;
  margin-bottom: 5px;
}
.list a {
  float: left;
}
.list span {
  float: right;
}

```
特别方便有木有？
## @argument变量
@argument这个用的不是特别多，用法如下：
```LESS
.br(@w:30px,@c:red,@sytle:solid){
	border:@arguments;// 当在这里需要用的所有的三个参数，又懒得三个都在这里再写一遍的时候，就使用@arguments变量
	height:@w;
}
.bor{
	.br();
}

.bor1{
	.br(40px);
}

// 输出如下：
.bor {
  border: 30px #ff0000 solid;
  height: 30px;
}
.bor1 {
  border: 40px #ff0000 solid;
  height: 40px;
}

```
## 避免编译、!important
有时候，出于各种原因，不希望LESS编译器编译某些内容，这时候只需要把不希望编译的内容用单引号或者双引号引起来，再在前面加上~即可
```LESS
.test{
	width:~"(300px-20px)";
}

// 输出
.test {
  width: (300px-20px);
}

```
同时，也可以像正常写CSS那样写!important

