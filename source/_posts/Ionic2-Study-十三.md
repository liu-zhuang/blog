---
title: Ionic2-Study-十三
date: 2017-01-16 21:28:19
tags: Ionic2 Angular2 
---
# Inoic2 Native 之Image Picker
Ionic的常用组件学习好之后，要学习一些Native Component了，这些组件都是通过PhoneCap的Cordova来访问原生的设备功能的。

要想使用Image Picker，首先就要安装Image Picker组件
使用以下命令安装Image Picker
> ionic plugin add https://github.com/Telerik-Verified-Plugins/ImagePicker

更多关于Image Picker访问Github:[地址]( https://github.com/wymsee/cordova-imagePicker)


# 基本用法
首先在头部导入`ImagePicker`:

``` typescript
import { ImagePicker } from 'ionic-native';
```

该组件有以下三个方法：
- getPictures(options) :顾名思义，就是选取照片
- hasReadPermission() : 判断是否有读取权限 (android用)
- requestReadPermission() : 请求读取权限 (android用)

## getPictures详解
Promise和箭头函数的写法：
``` javascript
ImagePicker.getPictures(options).then((results) => {
for (var i = 0; i < results.length; i++) {
console.log('Image URI: ' + results[i]);
}
}, (err) => { });
```

该方法的options属性如下：
- maximumImagesCount (number) :一次能选择的最大数量的图片 
- width              (number) 图片被允许的最大的宽度     
- height             (number) 图片被允许的最大的高度     
- quality            (number) 图片质量，默认是100       


注：maximumImagesCount实际测试ios不起作用，具体什么问题还要再研究



## 示例
在前台html页面中：


``` html
<ion-row>
	<img src="{{userInfo.Url}}" (click)="uploadPic()" />
</ion-row>
```


ts文件中：
``` typescript
uploadPic(){
var opt = {
maxImagesCount:1,
width:100,
height:100,
quality:50
};
ImagePicker.getPictures(opt).then((results)=>{
for (var i = 0; i < results.length; i++) {
this.userInfo.Url = results[i];
}
},
(err)=>{

});
}
```

效果就是点击图片跳转到系统相册中，选中之后更新图片
![preview](img1.png)