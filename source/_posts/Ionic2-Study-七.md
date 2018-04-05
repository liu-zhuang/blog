---
title: Ionic2 Study(七)
date: 2016-12-04 22:48:36
tags: Ionic2 Angular2
---
# 前言
已经学了Tabs,Tab,Button,Input,组件，现在就可以做一个简单的Login页面了

# 新增一个Tab页
新增一个tab页需要做以下几步操作：
1. 增加`<ion-tab></ion-tab>`标签
``` html
<ion-tab [root]="tabLogin" tabTitle="Login" tabIcon="person" ></ion-tab>
```
2. 在pages文件夹下分别增加`login.html`,`login.ts`和`login.scss`三个文件
3. 在`login.ts`页面中写入以下代码:
``` typescript
import { Component } from '@angular/core';
import { NavController } from 'ionic-angular';
@Component({
	selector:'login-page',
	templateUrl:'login.html'
})

export class LoginPage {
	constructor(public NavCtrl:NavController){
	}
}
```
4. 在app/app.module.ts中做以下修改
先在头部引入login
``` typescript 
import { LoginPage } from '../pages/login/login';
```
在`declarations`中增加`LoginPage`,同时在`entryComponents`中增加`entryComponents`
5. 在tabs.ts中做以下修改:
在头部导入login
``` typescript
import { LoginPage } from '../login/login';
```
在`TabsPage`中增加：
``` typescript
tabLogin:any = LoginPage;
```
6. 现在login这个tab页就增加好了 

# login的前台页面
代码如下：
``` html
<ion-header>
	<ion-navbar>
		<ion-title>Login In Page</ion-title>
	</ion-navbar>
</ion-header>
<ion-content padding>
	<ion-list>
		<ion-item>
			<ion-label floating color="danger">UserName</ion-label>
			<ion-input type="text" [(ngModel)]="userInfo.UserName" clearInput></ion-input>
		</ion-item>
		<ion-item>
			<ion-label floating color="danger">Password</ion-label>
			<ion-input type="password" [(ngModel)]="userInfo.PassWord" clearInput></ion-input>
		</ion-item>
		<ion-item>
			<button ion-button color="danger" (click)="btnClick()" block>Login</button>
		</ion-item>
	</ion-list>
</ion-content>
```
上述代码中的`(click)`和`[(ngModel)]`均为angular2的使用方式。

# login页面的后台代码
代码如下：
``` typescript
import { Component } from '@angular/core';
import { NavController,LoadingController } from 'ionic-angular';
import { HomePage } from '../home/home';

@Component({
	selector:'login-page',
	templateUrl:'login.html'
})

export class LoginPage {
	constructor(public NavCtrl:NavController,public LoadCtrl:LoadingController){
	}
	userInfo:UserInfo={
		UserName:'liuzhuang',
		PassWord:'111111'
	}

	loadDefault(){
		let loading = this.LoadCtrl.create({
			content:"loading...",//loading框显示的内容
			dismissOnPageChange:true, // 是否在切换页面之后关闭loading框
			showBackdrop:false 是否显示遮罩层
		});
		loading.present();// 弹出load框
		setTimeout(()=>{
			this.NavCtrl.push(HomePage);//跳转页面
		},1000);
		setTimeout(()=>{
			loading.dismiss();
		},3000);
		// 上面这段代码先是在按下按钮1000毫秒之后挑战页面，再在3000毫秒之后关闭loading框
		// 但是因为设置了切换页面之后关闭loading框，因此在切换页面后则关闭loading框
	}

	loadCustom(){
		let loading = this.LoadCtrl.create({
			spinner:"dots",// apinner既是loading框上的图标
			// content:`<div class="custom-spinner-container">
			// <div class="custom-spinner-box"></div>
			// </div>`,
			duration:5000 // loading框持续的时间，默认在触发DidDismiss之后关闭，除非设置了该属性
		});
		loading.onDidDismiss(()=>{
			console.log("Dismissed loading");
		});
		loading.present();
	}

	loadText(){
		let loading = this.LoadCtrl.create({
			spinner:"hide",
			content:"loading",
			duration:3000
		});
		loading.present();

	}

	btnClick(){
		this.loadDefault();
		// this.loadText();
		// this.loadCustom();
	}
}

export class UserInfo{
	UserName:string;
	PassWord:string;
}
```
上述代码主要有以下几个组成部分：
1. 声明了`UserInfo`类
2. 声明了`UserInfo`类的实例`userInfo`，并赋予初值，同时实现和前台两个input双向数据绑定
3. 初始化loading controller

# 源码
[地址](https://github.com/nicky-lau/Ionic2-demo)

