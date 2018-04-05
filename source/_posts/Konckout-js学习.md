---
title: Konckout.js学习(一)
date: 2016-09-24 19:24:33
tags: javascript knockout mvm
---
# 前言
knockout.js火的时候为对前端开发并不感冒，最近对前端开发感兴趣的时候在学更火的angular,可是造化弄人最近公司新项目选的是knockoutjs，没办法也只能学习一下了，好在技多不压身，反正人已经很胖了，多学一样东西体重不会更重。 －。－

# 官网教程
先贴下地址：[官网教程地址](learn.knockoutjs.com)
虽然中文也有汤姆大叔写的教程，但是版本实在太老了[汤姆大叔的链接](http://www.cnblogs.com/TomXu/archive/2011/11/21/2257154.html)
好在官网现在的教程也是由浅入深，浅显易懂，但是比较坑爹的是它引用的jQuery是google的cdn。于是不翻墙是打不开的。。
好吧，话不多说，就在官网从头开始学起吧。
官网的教程是分以下五步的：
1. Introduction: Getting started with MVVM and knockout.js
2. Working with List and Collections: Building a dynamic UI where elements are added or removed
3. Single Page Application: Enabling navigation within the page,with back/forward support
4. Creating Custom bindings: Gaining more control over the UI,and integrating with third-part Components
5. Loading and saving Data: Ways to integrate your client-side code with your sever-side code

## Introduction 简介－开始使用MVVM模式和knockout.js
和angular类似，knockout(以下简称ko)也是使用MVVM模式的，即Model-View-ViewModel，和ng有所不同的是，ng会使用ng-app来限定controller的作用范围，同时一个页面可以用多个controller,但是ko一个页面只能用一个viewmodel。
吐槽结束，开始最基础的hello，world。
```html
<p data-bind="text:hello" ></p>	
<input data-bind="value:hello" />
<script>
	var appViewModel = function(){
		var self = this;
		self.hello = "Hello World!"
	};
	ko.applyBindings(new appViewModel());
</script>

```
如上述代码所见，在js中声明一个ViewData的构造函数，然后再把这个构造函数的实例传给ko.applyBindings()这个方法，就实现了view和appViewModel的绑定。页面上p标签内会自动出现"Hello World!",输入框中也会出现同样内容。
然后在html标签上使用`data-bind="text:hello"`的方式来实现和viewmodel中数据的绑定。
注意一点，使用text绑定是将值写在html标签中间,`<p>text</p>`,使用value绑定则是在html标签的value属性中赋值`<p value='xx'></p>`。
但是这个时候，viewmodel中的属性仅仅只是无格式的JavaScript字符串，当属性发生变化的时候，并没有办法通知任何人发生了变化，因此还是静态UI。
（好吧，这里是去尝试意译官方的一段文档，整理了几遍还是感觉不够信达雅，就这样吧）简单的说，上面的代码，只实现了单向数据绑定。
如果想要实现双向数据绑定，需要做以下这个操作。
```html 
<p data-bind="text:hello" ></p>	
<input data-bind="value:hello" />
<script>
	var appViewData = function(){
		var self = this;
		// 在这里，把hello变为observable的
		self.hello =  ko.observable("Hello World!");
	};
	ko.applyBindings(new appViewData());
</script>
```
这样，当我在input框中修改hello的值之后，p标签的内容也会随之改变（即实现了双向数据绑定）
但是这里还是比不上ng，因为ko的双向数据绑定需要鼠标离开输入框才会同步，而ng在你输入的同时，实时就进行了双向绑定。

注意：当viewmodel中的属性被observable化了之后，他们就被变成了一个函数，我的理解是类似于{getter;setter;}的函数。当需要使用它们的时候，需要采用函数调用的方式：`self.hello()`。
接下来，再介绍一个ko中的常用方法，`ko.computed(func,thisObj)`，这个方法有两个参数，第一个就是执行的函数，第二个就是传入的对象，改变作用域用的，类似于js中的call。
computed observables依赖一个或者多个观察者（observable），并且当这些依赖观察者发生变化时也会随之发生变化。
[computed详解](http://www.xiaoboy.com/detail/2015031517.html)
举例如下：
```html 
<p data-bind="text:sayHello" ></p>	
<input data-bind="value:hello" />
<input data-bind="value:name" />

<script>
	var appViewData = function(){
		var self = this;
		self.hello =  ko.observable("Hello World!");
		self.name = ko.observable("lz");
		self.sayHello = ko.computed(function(){
			return this.hello() + " " + this.name();
			// 如果hello改变不需要更新则可以用peek方法
			//return this.hello.peek() + " " + this.name();
		},self);
	};
	ko.applyBindings(new appViewData());
</script>
```
如上面代码所示，sayHello方法将把hello和name的值拼接起来。当然了，computed()方法也是会生成可双向数据绑定的observable的function。
最后再写一个demo，来实现动态修改viewModel属性的方法
``` HTML
<p data-bind="text:sayHello" ></p>	
<input data-bind="value:hello" />
<input data-bind="value:name" />
<!-- 增加一个按钮 -->
<button data-bind="click:upperName">upper</button>
<script>
	var appViewData = function(){
		var self = this;
		self.hello =  ko.observable("Hello World!");
		self.name = ko.observable("lz");
		self.sayHello = ko.computed(function(){
			return this.hello() + " " + this.name();
		},self);
		// 当按下upper按钮时，把name变为大写
		self.upperName = function(){
			var currentValue = self.name();
			self.name(currentValue.toUpperCase());
		};
	};
	ko.applyBindings(new appViewData());
</script>
```
好了，Introduction的教程就结束了。
[源代码](https://github.com/nicky-lau/Knockoutjs/tree/master/Introduction)