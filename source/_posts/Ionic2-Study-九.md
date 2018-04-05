---
title: Ionic2 Study(九)
date: 2016-12-07 23:06:53
tags: Ionic2 Angular2
---

# Ionic2 组件之Toast
和Alert组件类似，都是为了提示信息给用户看的，不过使用Toast不会像Alert那样会显示遮罩层。

## Options
|       Property      |   Type  | Default  |                      Description                       |
|---------------------|---------|----------|--------------------------------------------------------|
| message             | string  | --       | 显示的信息                                             |
| duration            | number  | --       | 提示框持续的时间,如果不设置则持续到dismiss()事件被调用 |
| position            | string  | 'bottom' | 提示框出现的位置，可选的有：`top`,`middle`,`bottom`    |
| showCloseButton     | boolean | false    | 是否显示关闭按钮                                       |
| closeButtonText     | string  | 'Close'  | 关闭按钮上显示的文本                                   |
| dismissOnPageChange | boolean | false    | 切换页面时是否关闭提示框                               |
| cssClass            | string  | --       | 额外的样式                                             |


## demo
使用方式如下：
``` typescript
presentToast(){
let toast = this.toastCtrl.create({
message:'this is a toast',
duration:3000,
position:"bottom",
showCloseButton:true,
closeButtonText:'x',
cssClass:'danger',
dismissOnPageChange:true
});
toast.present();
}
```

# Ionic2 组件之Grid
和现在html流行的自适应栅格布局类似，Ionic也是采用了grid来进行布局
简单的用法如下：
``` html 
<ion-grid>
	<ion-row>
		<ion-col width-10>This column will take 10% of space</ion-col>
	</ion-row>
</ion-grid>
```
可以看到和bootstrap非常类似,因此offset肯定也是有的了

## 栅格大小
| Property | width(百分比) |
|----------|---------------|
| width-10 | 10%           |
| width-20 | 25%           |
| width-33 | 33.3333%      |
| width-50 | 50%           |
| width-67 | 66.6666%      |
| width-75 | 75%           |
| width-80 | 80%           |
| width-90 | 90%           |

## demo
``` html 
<ion-grid>
		<ion-row>
			<ion-col width-10>width-10</ion-col>
			<ion-col width-20>width-20</ion-col>
			<ion-col width-50>width-50</ion-col>
		</ion-row>
		<ion-row>
			<ion-col width-33>width-33</ion-col>
			<ion-col width-33>width-33</ion-col>
			<ion-col width-33>width-33</ion-col>
		</ion-row>
		<ion-row>
			<ion-col width-20 offset-33 center>width-33</ion-col>
		</ion-row>
		<ion-row wrap>aaa</ion-row>
		<ion-row wrap>aaa</ion-row>
		<ion-row wrap>aaa</ion-row>
		<ion-row center>
			<ion-col width-33>width-33</ion-col>
			<ion-col width-33 >aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa</ion-col>
		</ion-row>
		<ion-row baseline>
			<ion-col width-33>width-33</ion-col>
			<ion-col width-33 >bbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbb</ion-col>
		</ion-row>
	</ion-grid>
```