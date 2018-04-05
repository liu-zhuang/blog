---
title: Ionic2 Study(三)
date: 2016-11-24 20:58:52
tags: Ionic Ionic2 Tabs
---
# Ionic2组件之Tabs
常用的app都是采用tabs布局形式的，默认的ionic2的也是采用tabs的。

## 概述
Tabs能够非常简单的在不同的页面间进行导航，Tabs组件写作`<ion-tabs>`,同时也是ion-tab的容器。

## 属性
ion-tabs组件有以下常用属性：
- Placement:tabsPlacement
- Layout:tabsLayout
- Selecting a Tab:selectedIndex

### Color
可以通过color属性为tabs设置背景色，当然，通常使用的是全局变量的颜色。
诸如："primary", "secondary", "danger"

### tabsHighlight
可以通过tabsHighlight来设置是否高亮选中的tab标签
默认为false

### Placement
Tabs可以出现在屏幕的上方和下方
使用该属性来设置，写作：tabsPlacement,可选值有：top,bottom
默认值为bottom
同时也可以写在app的config文件里，这个是后话。
``` html
<ion-tabs #mainTabs tabsPlacement="top">
</ion-tabs>
```
效果图：
![top](ionic1.png)

### Layout
每个Tab上都会有图标和文字，图标和文字的位置设置使用tabsLayout属性来设置，图标和文字的位置组合有以下几种方式：
- icon-top:图标在上
- icon-lef:图标在左
- icon-right:图标在右
- icon-bottom:图标在下
- icon-hide:隐藏图标
- title-hide:文字隐藏

隐藏文字的效果图如下：是不是很像微信的朋友圈？
![效果图](ionic2.png)

### Selecting a Tab
可以通过在ion-tabs标签上增加selectedIndex属性来初始化默认选中的tabs，(从0开始)如：
``` html
<ion-tabs #mainTabs tabsLayout="title-hide" selectedIndex="2">
</ion-tabs>
```
效果图如下：
![selectedIndex](ionic3.png)

但是这样做相当于hard code写死了，所以一般采用调用Tabs实例等select()方法，来动态选择“选中的tab"
比如：
``` typescript
import  { Component,ViewChild }    from '@angular/core';
import  { HomePage }     from '../home/home';
import  { AboutPage }    from '../about/about';
import  { ContactPage }  from '../contact/contact';
import  { Tabs }         from 'ionic-angular';

@Component({
	templateUrl: 'tabs.html'
})
export class TabsPage {
	@ViewChild('mainTabs') tabRef:Tabs;
	// this tells the tabs component which Pages
	// should be each tab's root Page
	tab1Root: any = HomePage;
	tab2Root: any = AboutPage;
	tab3Root: any = ContactPage;

	constructor() {
	}

	ionViewDidEnter(){
		let mainTabs = this.tabRef;
		mainTabs.select(1);
	}
}

```
首先，需要在头部，从"angular/core"中将ViewChild引入进来，`import  { Component,ViewChild }    from '@angular/core';`，接着，从"ionic-angular"中将"Tabs"组件引入进来,`import  { Tabs }         from 'ionic-angular';`,接下来，就可以使用了，使用方式如下：
``` typescript
@ViewChild('mainTabs') tabRef:Tabs;
ionViewDidEnter(){
	let mainTabs = this.tabRef;
	mainTabs.select(1);
}
```

上述代码大致意思是：
- 先使用ViewChild方法，通过id，找到Tabs的实例：mainTabs
- Tabs实例使用tabs的方法：select(),动态设置选中的tabs

完整代码如下：
``` typescript
import  { Component,ViewChild }    from '@angular/core';
import  { HomePage }     from '../home/home';
import  { AboutPage }    from '../about/about';
import  { ContactPage }  from '../contact/contact';
import  { Tabs }         from 'ionic-angular';

@Component({
	templateUrl: 'tabs.html'
})
export class TabsPage {
	@ViewChild('mainTabs') tabRef:Tabs;
	// this tells the tabs component which Pages
	// should be each tab's root Page
	tab1Root: any = HomePage;
	tab2Root: any = AboutPage;
	tab3Root: any = ContactPage;

	constructor() {
	}

	ionViewDidEnter(){
		let mainTabs = this.tabRef;
		mainTabs.select(1);
	}
}
```
当然，还可以在一个子组件中，通过调用父页面的实例方法来实现切换tab页

``` typescript
import { Component } from '@angular/core';

import { NavController } from 'ionic-angular';

@Component({
	selector: 'page-about',
	templateUrl: 'about.html'
})
export class AboutPage {

	constructor(public navCtrl: NavController) {
	}
	ionViewDidEnter(){
		this.navCtrl.parent.select(0);
	}
}

```

在tabs导航的某一页面中，默认会导入`NavController`,它的实例方法中有parent,假设它的parent就是tabs,那么就可以使用`this.navCtro.parent.select()`方法来进行切换tabs选择了。
上面代码的功能是在进入页面后，自动切换到第一个tab页。

## 实例成员
调试抓取一下Tab的实例看看都有哪些成员：
![1](ionic4.png)
![2](ionic5.png)

从上面可以看到Tabs的实例成员。
常用的有：select,previousTab,getByIndex,getSelected

另外，可以看到，有个ionChange方法，说明了它的change事件的触发器，具体实现如下（其实就是ng2的绑定事件）：
html中声明事件：

```html
<ion-tabs #mainTabs (ionChange)="changeTabs()" color="danger" tabsHighlight="true">
	<ion-tab [root]="tab1Root" tabTitle="Home" tabIcon="home"></ion-tab>
	<ion-tab [root]="tab2Root" tabTitle="Friends" tabIcon="aperture" tabBadge="3" tabBadgeStyle="danger"></ion-tab>
	<ion-tab [root]="tab3Root" tabTitle="Contact" tabIcon="contacts"></ion-tab>
</ion-tabs>
```

在ts文件中：
``` typescript
import  { Component,ViewChild }    from '@angular/core';
import  { HomePage }     from '../home/home';
import  { AboutPage }    from '../about/about';
import  { ContactPage }  from '../contact/contact';
import  { Tabs }         from 'ionic-angular';

@Component({
	templateUrl: 'tabs.html'
})
export class TabsPage {
	@ViewChild('mainTabs') tabRef:Tabs;
	// this tells the tabs component which Pages
	// should be each tab's root Page
	tab1Root: any = HomePage;
	tab2Root: any = AboutPage;
	tab3Root: any = ContactPage;

	constructor() {
		// this.changeTabs = function(){
			// 	alert(1)
			// };
		}

		changeTabs = function(){
			console.log("tab changed");
		};

		ionViewDidEnter(){
			let mainTabs = this.tabRef;
			debugger;
			mainTabs.select(1);
		}

	}
```
可以看到，`changeTabs`方法，既可以卸载构造函数中，也可以直接放在外面，ng2的标准写法应该是写在外面？
ng2还在学习ing。。。将来再回来补充。。。

至此，tabs的常规用法就差不多了。。。
最后，惯例附上官网doc链接：
[官网链接](http://ionicframework.com/docs/v2/api/components/tabs/Tabs/)

