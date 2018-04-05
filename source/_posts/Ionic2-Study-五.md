---
title: Ionic2 Study(五)
date: 2016-11-30 22:31:51
tags: Ionic2 Angular2
---

# Ionic2 组件之Button
> Buttons are simple components in Ionic. They can consist of text and icons and be enhanced by a wide range of attributes.

button是简单的组件，包含文字和图标以及很多属性。

# 属性
先不管按钮的click事件，简单的学习下控制按钮样式的一些属性:

## color
顾名思义，就是控制按钮的颜色
``` html
<!-- Colors -->
	<button ion-button>Default</button>

	<button ion-button color="secondary">Secondary</button>

	<button ion-button color="danger">Danger</button>

	<button ion-button color="light">Light</button>

	<button ion-button color="dark">Dark</button>
```

多说明一句，上面使用的color,都是定义在`src/theme/variables.scss`文件中的
![variables](ionic2.png)

## shape
有以下三种形状：
- full
- block
- round

前两个我没有发现什么大的区别，好像都是撑满父容器，有了解区别的还请指教
round顾名思义就是圆角按钮的意思
``` html
	<!-- Shapes -->
	Shapes:
	<br>
	<table>
		<tr>
			<td>	
				<button ion-button block>Block Button</button>
			</td>
			<td></td>
		</tr>
	</table>
	
	<button ion-button round>Round Button</button>
```
## outline
使用该属性将使按钮只有边框。
``` html
<!-- Outline -->
	<button ion-button full outline>Outline + Full</button>

	<button ion-button block outline>Outline + Block</button>

	<button ion-button round outline>Outline + Round</button>
```

## Icons
在按钮中增加图标的属性,有以下三个值:
- icon-left  : 图标在文字左边
- icon-right ：图标在文字右边
- icon-only  ：只有图标

``` html
	<!-- Icons -->
	<button ion-button icon-left>
		<ion-icon name="star"></ion-icon>
		Left Icon
	</button>

	<button ion-button icon-right>
		Right Icon
		<ion-icon name="star"></ion-icon>
	</button>

	<button ion-button icon-only>
		<ion-icon name="star"></ion-icon>
	</button>
```

## Size
这个很简单，就是制定按钮的大小，有以下三个值：
- Large
- Default
- small
大，默认，小
``` html
	<!-- Sizes -->
	<button ion-button large>Large</button>

	<button ion-button>Default</button>

	<button ion-button small>Small</button>
```

## 高级用法
其实就是和angular2一起使用动态设置样式的用法
直接上示例：
``` html 
<button ion-button [color]="isDanger?'danger':'primary'">advanced</button>

	<!-- Bind the color and round inputs to an expression -->
	<button ion-button [color]="myColor" [round]="isRound">
		Secondary (Round)
	</button>

	<!-- Bind the color and clear inputs to an expression -->
	<button ion-button [color]="isSecondary ? 'secondary' : 'primary'"  [clear]="isClear">
		Primary (Clear)
	</button>

	<!-- Bind the color, outline and round inputs to an expression -->
	<button ion-button [color]="myColor2" [outline]="isOutline" [round]="isRound">
		Dark (Solid + Round)
	</button>
```
在ts中如下使用：
``` TypeScript
export class ButtonPage {
	constructor(public navCtrl: NavController,public navParams:NavParams) {

	}
	isDanger: boolean = true;
	isSecondary: boolean = false;
	isRound: boolean = true;
	isOutline: boolean = false;
	isClear: boolean = true;
	myColor: string = 'secondary';
	myColor2: string = 'dark';
}
```
# 预览
![preview](ionic1.png)