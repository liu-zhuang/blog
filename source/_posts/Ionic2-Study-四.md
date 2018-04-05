---
title: Ionic2 Study(四)
date: 2016-11-27 21:57:31
tags: Ionic2 Angular2 
---
# Ionic2 组件之Tab
学了Tabs更少不了Tab组件，Tab组件写作`<ion-tab>`,必须要放置在Tabs组件内，是NavController的一种。

# 基本用法
Tab有以下几个基本属性和事件:

|        Attr        |   Type  |           Desc          |
|--------------------|---------|-------------------------|
| [root]             | Page    | 指定tab希望加载的页面   |
| tabTitle           | string  | tab上显示的标题         |
| tabIcon            | string  | tab上显示的图标         |
| tabBadge           | string  | tab上显示的角标数字     |
| tabBadgeStyle      | string  | 角标数字的颜色          |
| enabled            | boolean | tab是否可用，默认为true |
| show               | boolean | tab是否显示             |
| [rootParams]       | object  | tab上传递的参数         |
| tabUrlPath         | string  | 点击tab时跳转的页面url  |
| swipeBackEnabled   | boolean | 是否支持滑动后退        |
| tabsHideOnSubPages | boolean | 在子页面是否隐藏        |
| (ionSelect)        | 方法    | tab select 方法         |

## 属性和事件详解
示例html代码如下:
``` html
<ion-tabs #mainTabs (ionChange)="changeTabs()" color="danger" tabsHighlight="true">
	<ion-tab [root]="tab1Root" [rootParams]="homeParams" tabTitle="Home" tabIcon="home"></ion-tab>
	<ion-tab [root]="tab2Root" (ionSelect)="selectFriend()" tabTitle="Friends" tabIcon="aperture" tabBadge="3" tabBadgeStyle="danger"></ion-tab>
	<ion-tab [root]="tab3Root" enabled="false"  swipeBackEnabled="true" tabTitle="Contact" tabIcon="contacts"></ion-tab>
</ion-tabs>
```
示例typeScript脚本代码如下：
``` TypeScript
import  { Component,ViewChild }    from '@angular/core';
import  { HomePage }     from '../home/home';
import  { AboutPage }    from '../about/about';
import  { ContactPage }  from '../contact/contact';
import  { Tabs,ModalController } from 'ionic-angular';

@Component({
	templateUrl: 'tabs.html'
})
export class TabsPage {
	@ViewChild('mainTabs') tabRef:Tabs;
	// this tells the tabs component which Pages
	// should be each tab's root Page
	tab1Root: any = HomePage;//为tab标签指定导航至的页面
	tab2Root: any = AboutPage;
	tab3Root: any = ContactPage;
	// 指定参数,在tab指向的页面可以读取到该参数
	homeParams:any = {
		user1:"admin",
		user2:"ionic"
	};
	constructor(public modalCtrl: ModalController) {
	}
	changeTabs = function(){
		console.log("tab changed");
	};
	// 选中tab页后的事件
	selectFriend(){
		// 声明一个modal
		let modal = this.modalCtrl.create(ContactPage);
		// 弹出modal
		modal.present();
	}
	ionViewDidEnter(){
		let mainTabs = this.tabRef;
		mainTabs.select(1);
	}
}
```
home页使用参数示例代码如下：
``` TypeScript
import { Component } from '@angular/core';
// 引入NavParams
import { NavController,NavParams } from 'ionic-angular';

@Component({
	selector: 'page-home',
	templateUrl: 'home.html'
})
export class HomePage {
	// 在这里指定navParams
	constructor(public navCtrl: NavController,public navParams:NavParams) {
	}
	ionViewWillEnter(){
		// 使用this.navParams.data可以读取tab页传过来的参数
		console.log("NavParams:"+this.navParams.data.user1);
		console.log("NavParams:"+this.navParams.data.user2);
	}
}
```
## 预览图
![预览](ionic1.png)

