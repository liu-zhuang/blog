---
title: Ionic2 Study(十一)
date: 2016-12-13 22:06:38
tags: Ionic2 Angular2
---
# Ionci2组件之List
> The List is a widely used interface element in almost any mobile app, and can include content ranging from basic text all the way to buttons, toggles, icons, and thumbnails.
Both the list, which contains items, and the list items themselves can be any HTML element.
Using the List and Item components make it easy to support various interaction modes such as swipe to edit, drag to reorder, and removing items.

List组件被广泛的用于app，可以包含文字，按钮，滑块,图标和图片。
List和List中的item都是HTML元素。
使用List和Item组件可以非常方便的支持诸如滑动编辑，拖动排序，或者移除之类的交互操作。

# List类型
`ion-list`包含以下几种类型：
- Basic Lists
- Inset List
- List Dividers
- List Headers
- Icon List
- Avatar List
- Multi-line List
- Sliding List
- Thumbnail List
接下来就分别演示有何区别

## Basic List
顾名思义，就是最基础的应用，在`ion-list`中嵌套`ion-item`来使用

先做一些基础准备：
在后台定义一个仓库类
``` typescript
export class WareHouse{
	id:number;
	name:string;
	desc:string;
	img:string;
	constructor(id:number,name:string,desc:string,img:string){
		this.id = id;
		this.name = name;
		this.desc = desc;
		this.img  = img;
	}
}
```
然后模拟通过api获取到了一个`WareHouse`类型的数组
``` typescript
	wareHouseList:WareHouse[]=[
	new WareHouse(1,'上海仓','徐汇仓库','img/1.jpg'),
	new WareHouse(2,'北京仓','一环仓库','img/2.jpg'),
	new WareHouse(3,'北京二仓','二环仓库','img/3.jpg'),
	new WareHouse(4,'北京三仓','三环仓库','img/4.jpg'),
	new WareHouse(5,'广州仓','小蛮腰','img/5.jpg'),
	new WareHouse(6,'杭州仓','西湖美景','img/6.jpg'),
	new WareHouse(7,'苏州仓','楼外楼','img/7.jpg'),
	new WareHouse(8,'扬州仓','汤包','img/8.jpg'),
	new WareHouse(9,'上海二仓','闵行仓库','img/9.jpeg'),
	new WareHouse(10,'上海三仓','浦东仓库','img/10.jpg')
	];
```
接下来在前台通过angular2来把这个数组上的数据循环绑定到`ion-list`上
```html
	<ion-list>
		<ion-item *ngFor="let wh of wareHouseList" (click)="itemClick($event,wh)">
			{{wh.name}}
		</ion-item>
	</ion-list>
```
点击item时触发的click事件：
``` typescript
	itemClick(event,item:WareHouse):void{
		alert(item.name);
	}
```
预览：
![basic list](list1.png)

注意：上述代码的循环绑定如`*ngFor`和`(click)`使用的是ng2的语法，强类型使用的是typescript的语法

### No Lines
可以看到，每个List Item之间都是有一根线划分开的，可以为`ion-list`增加一个`no-lines`属性来移除这些分割线。
``` html
	<ion-list no-lines>
		<ion-item *ngFor="let wh of wareHouseList" (click)="itemClick($event,wh)">
			{{wh.name}}
		</ion-item>
	</ion-list>
```
![no-lines](list2.png)
这样就移除了分割线

## Inset List
List本来是没有外边距的，可以通过为List增加`inset`属性来为List增加外边距
``` html
	<ion-list inset>
		<ion-item *ngFor="let wh of wareHouseList" (click)="itemClick($event,wh)">
			{{wh.name}}
		</ion-item>
	</ion-list>
```
![inset](list3.png)
可以很明显的看出现在list和上面的title有了margin
 
## List Dividers
当希望把Item分组时，可以使用`<ion-item-group>`来替代`<ion-list>`,使用`<ion-item-divider>`组件来作为分组的组名
示例：
``` html
	<ion-item-group>
		<ion-item-divider color="light">A</ion-item-divider>
		<ion-item>Angola</ion-item>
		<ion-item>Argentina</ion-item>
		<ion-item>Australia</ion-item>
		<ion-item-divider color="light">B</ion-item-divider>
		<ion-item>Bangladesh</ion-item>
		<ion-item>Belarus</ion-item>
		<ion-item>Belgium</ion-item>
	</ion-item-group>
```
其实希望实现的效果是`A`和`B`是有灰色背景的
![divider](list4.png)
但是实际上直接生成的是没有的（至少在chrome是如此），因为ionic框架生成出来的代码如下：
``` html
<ion-item-divider class="item item-md item-md-light" color="light" ng-reflect-color="light">
```
其中`item-md`是有`background-color`属性的，设置为了`#fff`也就是白色，同时它的优先级比`item-md-light`，因此就没有显示出来灰色的背景色
这个还要再研究下。

## List Headers
每个list都可以有个header在顶部
示例：
``` html
	<ion-list>
		<ion-list-header>WareHouse</ion-list-header>
		<ion-item *ngFor="let wh of wareHouseList" (click)="itemClick($event,wh)">
			{{wh.name}}
		</ion-item>
	</ion-list>
```
![list header](list5.png)

## Icon List
可以为item增加图标来吸引眼球，图标的位置由`item-left`和`item-right`来控制，图标默认为大小为“小”，也可以通过为图标增加`large`属性来将图标变大
``` html
	<ion-list>
		<ion-item>
			<ion-icon name="leaf" item-left></ion-icon>
			item-left
		</ion-item>
		<ion-item>
			<ion-icon name="rose" item-right></ion-icon>
			item-right
		</ion-item>
		<ion-item>
			<ion-icon name="rose" item-right large>large</ion-icon>
		</ion-item>
	</ion-list>
```

![Icon list](list6.png)

## Avatar List/Multi-line List/Thumbnail List
这三种都是多行的，区别无非就是昨天是个小图／没有图／和大图
```
	<ion-list no-lines>
		<ion-item *ngFor="let wh of wareHouseList">
			<ion-avatar item-left>
				<img src={{wh.img}}>
			</ion-avatar>
			<h2>{{wh.name}}</h2>
			<p>{{wh.desc}}</p>
		</ion-item>
	</ion-list>
```
Thumbnail list就是把`ion-avatar`换成`ion-thumbnail`

![avatar](list7.png)

## Sliding List
顾名思义就是list中的item可以向左或向右滑动来出现隐藏的按钮。
想实现这个效果，需要首先在`<ion-list>`内部嵌套`ion-item-sliding`这个组件，同时在每个`<ion-item>`下面增加`<ion-item-options>`组件，
直接看示例：
``` html
<ion-list>
		<ion-item-sliding>
			<ion-item>
				<ion-avatar item-left>
					<img src="img/1.jpg">
				</ion-avatar>
				<h2>Slimer</h2>
			</ion-item>
			<ion-item-options side="left">
				<button ion-button color="primary">
					<ion-icon name="text"></ion-icon>
					Text
				</button>
				<button ion-button color="secondary">
					<ion-icon name="call"></ion-icon>
					Call
				</button>
			</ion-item-options>
			<ion-item-options side="right">
				<button ion-button color="primary">
					<ion-icon name="mail"></ion-icon>
					Email
				</button>
			</ion-item-options>
		</ion-item-sliding>
	</ion-list>
```
这样就实现了从左向右滑时出现“Text”和“Call”，从右向左滑时出现“Email”
![sliding](list8.png)
![sliding2](list9.png)
