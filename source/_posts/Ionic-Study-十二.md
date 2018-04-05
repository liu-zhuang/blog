---
title: Ionic2 Study(十二)
date: 2016-12-15 23:55:30
tags: Ionic2 Angular2
---
# Ionic2组件之Card
> Cards are a great way to display important pieces of content, and are quickly emerging as a core design pattern for apps. They're are a great way to contain and organize information, while also setting up predictable expectations for the user. With so much content to display at once, and often so little screen realestate, cards have fast become the design pattern of choice for many companies, including the likes of Google, Twitter, and Spotify.
For mobile experiences, Cards make it easy to display the same information visually across many different screen sizes. They allow for more control, are flexible, and can even be animated. Cards are usually placed on top of one another, but they can also be used like a "page" and swiped between, left and right.

卡片是一种非常重要的呈现内容的方式。

有以下一些内容：
- Basic Cards
- Card Headers
- Card Lists
- Card Images
- Background Images
- Advance Cards

## Basic Usage
Cards最基本的用法就是`<ion-card>`里面包含`<ion-card-header>`和`<ion-card-content>`
示例代码：
``` html
<ion-card>
	<ion-card-header>
		固定资产卡片-header
	</ion-card-header>
	<ion-card-content>
		固定资产卡片-content
	</ion-card-content>
</ion-card>
```
如上所述，就是有标题和内容
![card1](card1.png)

## Lists in Cards
可以在Cards中增加`<ion-list>`来显示列表
示例代码：
``` html 
  <ion-card>
    <ion-card-header>
      Explore Nearby
    </ion-card-header>
    <ion-list>
      <button ion-item>
        <ion-icon name="cart" item-left></ion-icon>
        Shopping
      </button>
      <button ion-item>
        <ion-icon name="beer" item-left></ion-icon>
        Shopping
      </button>
    </ion-list>
  </ion-card>
```
可以看到和在其他地方用`<ion-list>`的方法一样
上面的代码中并没有把list放在`<ion-card-content>`中，如果放在content内的话，将会显示边框。
![card2](card2.png)

## Images In Cards
可以在`<ion-card>`中增加图片
示例代码：
``` html
  <ion-card>
    <img src="img/pic.jpg" />
    <ion-card-content>
      <ion-card-title>
        凯旋门
        <p>
          啦啦啦啦啦～～～～
        </p>
      </ion-card-title>
    </ion-card-content>   
  </ion-card>
```

![img1.png](img1.png)
如图所示，默认是流式布局，从上到下依次排列

## Background Images
也可以将图片作为背景图片，将文字浮于文字之上
其实只要通过css样式来控制即可
也就是把父级元素（即`<ion-card>`)设为相对定位，图片正常放置，文字设为相对定位即可

``` html 
<ion-card>
  <img src="img/pic.jpg"/>
  <div class="card-title">São Paulo</div>
  <div class="card-subtitle">41 Listings</div>
</ion-card>
```
将以下样式写在html页面同名的scss文件中：
``` scss
ion-card {
	position: relative;
	text-align: center;
}

.card-title {
	position: absolute;
	top: 36%;
	font-size: 2.0em;
	width: 100%;
	font-weight: bold;
	color: #fff;
}

.card-subtitle {
	font-size: 1.0em;
	position: absolute;
	top: 52%;
	width: 100%;
	color: #fff;
}
```

这样最终会将这些样式加在main.css文件中
想要增加样式还有另一种写法:
``` typescript
@Component({
	selector: 'page-contact',
	templateUrl: 'contact.html',
	styles:[`ion-card {
	position: relative;
	text-align: center;
}

.card-title {
	position: absolute;
	top: 36%;
	font-size: 2.0em;
	width: 100%;
	font-weight: bold;
	color: #fff;
}

.card-subtitle {
	font-size: 1.0em;
	position: absolute;
	top: 52%;
	width: 100%;
	color: #fff;
}`]
})
```

![img2](img2.png)

## Social Cards
最后可以通过将之前所有组件组合起来，模拟朋友圈这类社交app的样子
``` html 
<ion-card>
  <ion-item>
    <ion-avatar item-left>
      <img src="img/1.jpg">
    </ion-avatar>
    <h2>皮卡丘</h2>
    <p>Dec 28, 2016</p>
  </ion-item>
  <img src="img/pic.jpg" />
  <ion-card-content>
    <ion-card-title>
      凯旋门
      <p>
        啦啦啦啦啦～～～～
      </p>
    </ion-card-title>
  </ion-card-content>
  <ion-row>
    <ion-col>
      <button ion-button icon-left clear small color="danger">
        <ion-icon name="thumbs-up" ></ion-icon>
        <div >12 Likes</div>
      </button>
    </ion-col>
    <ion-col>
      <button ion-button icon-left clear small color="danger">
        <ion-icon name="text"></ion-icon>
        <div>4 Comments</div>
      </button>
    </ion-col>
    <ion-col center text-center>
      <ion-note>
        11h ago
      </ion-note>
    </ion-col>
  </ion-row>
</ion-card>
```
![img3](img3.png)

