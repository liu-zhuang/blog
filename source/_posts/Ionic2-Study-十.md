---
title: Ionic2 Study(十)
date: 2016-12-09 22:16:14
tags: Ionic2 Angular2
---
# Ionic2组件之Toolbar
> A Toolbar is a generic bar that is positioned above or below content. Unlike a Navbar, a toolbar can be used as a subheader. When toolbars are placed within an <ion-header> or <ion-footer>, the toolbars stay fixed in their respective location. When placed within <ion-content>, toolbars will scroll with the content.

toolbar可以放在内容的上面或者下面，不像Navbar组件，toolbar可以拿来作为wubheader使用。当toolbar被放在`<ion-header>`或`<ion-footer>`中时，toolbars显示在头部或者尾部，当它被放在`<ion-content>`中时，toolbars跟着内容一起滚动.

## Buttons in a toolbar
toolbar中的按钮需要放在`<ion-buttons>`元素中（除了menuToggle按钮)
`<ion-buttons>`有以下几个属性:
- start : 按钮显示在“开始”位置，也就是在ios系统中显示在左边，在安卓或winPhone的时候显示在右边
- end   : 按钮显示在“结束”位置，也就是在ios系统中显示在右边，在安卓或winPhone的时候显示在左边
- left  : 固定显示在左边
- right : 固定显示在右边
如：
``` html
<ion-toolbar> 
	<ion-title mode="ios">
		注册页面
	</ion-title>
	<ion-buttons start>
		<button ion-button color="primary" (click)="goBack()"><ion-icon name="undo"></ion-icon></button>
	</ion-buttons>
</ion-toolbar>
```

## Header / Footer Box Shadow
在安卓模式下，`<ion-header>`将会在下方出现阴影，`<ion-footer>`将会在上面出现阴影，可以用`no-shadow`来隐藏阴影。
如：
``` html 
<ion-footer no-shadow>
	<ion-toolbar>
		<ion-title>Footer</ion-title>
	</ion-toolbar>
</ion-footer>
```

## Toolbar Borders
文档中说，在ios模式下，`<ion-toolbar>`会产生border,因此需要使用`no-border`,`no-border-top`和`no-border-bottom`来分别消除上下border,上面的border或下面的border
但是我无论是在网页中还是XCode的Simulator中，都看不到任何border。

## demo
html:
``` html 
<ion-header>
	<ion-toolbar> 
		<ion-title mode="ios">
			注册页面
		</ion-title>
		<ion-buttons start>
			<button ion-button color="primary" (click)="goBack()"><ion-icon name="undo"></ion-icon>返回</button>
		</ion-buttons>
	</ion-toolbar>
</ion-header>
<ion-content>
	<ion-list>
		<ion-item>
			<ion-label floating color="danger">用户名</ion-label>
			<ion-input type="text" clearInput></ion-input>
		</ion-item>
		<ion-item>
			<ion-label floating color="danger">密码</ion-label>
			<ion-input type="text" clearInput></ion-input>
		</ion-item>
	</ion-list>



</ion-content>
<ion-footer no-shadow>
	<ion-toolbar>
		<ion-title>copyright liuzhuang 2016</ion-title>
	</ion-toolbar>
</ion-footer>
```
typescript:
``` typescript
import {Component} from '@angular/core';
import {NavController,ViewController} from 'ionic-angular';
@Component(
{
	selector:"register-page",
	templateUrl:"register.html"
})

export class RegisterPage{
	constructor(private NavCtrl:NavController,private ViewCtrl:ViewController){

	}
	goBack(){
		this.ViewCtrl.dismiss();
	}
}
```

## 完整demo
[地址](https://github.com/nicky-lau/Ionic2-demo)