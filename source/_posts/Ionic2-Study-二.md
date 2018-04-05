---
title: Ionic2-Study(二)
date: 2016-11-22 23:17:06
tags: Ionic Ionic2 
---
# Ionic页面的生命周期
[官方文档:Lifecycle events](http://ionicframework.com/docs/v2/api/navigation/NavController/)
![document](ionic1.png)

如官网文档所示，常用的生命周期有如下6个： 

|       Event       |                                               Desc                                               |
|-------------------|--------------------------------------------------------------------------------------------------|
| ionViewDidLoad    | 当页面加载的时候触发，仅在页面创建的时候触发一次，如果被缓存了，那么下次再打开这个页面则不会触发 |
| ionViewWillEnter  | 顾名思义，当将要进入页面时触发                                                                   |
| ionViewDidEnter   | 当进入页面时触发                                                                                 |
| ionViewWillLeave  | 当将要从页面离开时触发                                                                           |
| ionViewDidLeave   | 离开页面时触发                                                                                   |
| ionViewWillUnload | 当页面将要销毁同时页面上元素移除时触发                                                           |

在demo中加入以下代码：

``` typescript
ionViewLoaded(){
		debugger;
		console.log("1.0 homepage loaded!");
	}
	ionViewWillEnter(){
		console.log("2.0 homepage will Enter!");
	}

	ionViewDidEnter(){
		console.log("3.0 homepage did enter");
	}

	ionViewWillLeave(){
		console.log("4.0 homepage will leave");
	}

	ionViewDidLeave(){
		console.log("5.0 homepage did leave");
	}

	ionViewWillUnload(){
		console.log("6.0 homepage did leave");
	}
```

加载页面和切换页面并不会触发ionViewLoaded和ionViewWillUnload这两个方法。具体怎么才能触发还要再去研究。
但是ionViewLoaded方法可以使用angular的生命周期方法替换，如下所示：
``` typescript
	ngOnInit(){
		console.log("ng on init...");
	}
```
可以看到日志文件如下所示：

![log](ionic-log.png)


