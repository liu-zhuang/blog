---
title: Vue.js 购物车实战
date: 2017-02-18 20:15:01
tags: Vue.js 
---
# 前言
虽然Vue最强大的是组件化开发，但是其实多页面开发也蛮适合的。看了慕课网的使用Vue2.0开发购物车的教程之后，自己也实现了一遍。

# 初始化package.json
使用`npm init`命令来自动生成package.json文件。
会依次让你输入:
- name: 你的项目名称(默认会使用你的文件夹名称，不更改的话直接回车跳过)
- version: 版本号（默认1.0.0,不更改直接回车跳过)
- description: 项目描述（可为空，不写直接回车跳过)
- entry point: 入口文件（默认为index.js)
- test command: 测试命令行（回车跳过)
- git repository: git仓库地址（回车跳过)
- keywords: 关键字
- author: 作者
- license: 所用协议
接下来会让你预览package.json文件，输入`yes`即可生成package.json文件

因为该项目会用到`vue`,`axios`(在Vue2.0中取代vue-resource)和`http-server`(用来将文件夹转为服务器，避免跨域问题),
因此再使用以下命令，在package.json中把上面三个库引入进来
``` cmd
npm install vue --save-dev
npm install axios --save-dev
npm install http-server --save-dev
```
这样在项目文件夹下就会出现"node_modules"文件夹，我们需要的三个库也会下载下来了
同时，package.json文件中会多出以下这段内容：
``` json
  "devDependencies": {
    "axios": "^0.15.3",
    "http-server": "^0.9.0",
    "vue": "^2.1.10"
  }
```
这样，下次就可以直接使用`npm install`来安装所需的依赖的

接下来,再在package.json中修改"scripts"，增加以下内容:
``` json
"scripts": {
  "start":"http-server -a localhost -p 8080"
}
```
这样就可以通过`npm start`的方式来启动项目了，在浏览器输入`localhost:8080`即可访问

附上`http-server`的参数说明:
-p 端口号 (默认 8080)
-a IP 地址 (默认 0.0.0.0)
-d 显示目录列表 (默认 'True')
-i 显示 autoIndex (默认 'True')
-e or --ext 如果没有提供默认的文件扩展名(默认 'html')
-s or --silent 禁止日志信息输出
--cors 启用 CORS via the Access-Control-Allow-Origin header
-o 在开始服务后打开浏览器
-c 为 cache-control max-age header 设置Cache time(秒) , e.g. -c10 for 10 seconds (defaults to '3600'). 禁用 caching, 则使用 -c-1.
-U 或 --utc 使用UTC time 格式化log消息
-P or --proxy Proxies all requests which can't be resolved locally to the given url. e.g.: -P http://someurl.com
-S or --ssl 启用 https
-C or --cert ssl cert 文件路径 (default: cert.pem)
-K or --key Path to ssl key file (default: key.pem).
-r or --robots Provide a /robots.txt (whose content defaults to 'User-agent: *\nDisallow: /')
-h or --help 打印以上列表并退出

# 购物车
在Vue的生命周期钩子函数中，`mounted`就相当于jQuery的`.ready`方法,但是使用`mounted`并不能保证钩子函数中的`this.$el`在document中，为此还需要引入`Vue.nextTick/vm.$nextTick`，见[官方文档](https://cn.vuejs.org/v2/guide/migration.html#ready-替换)
所以购物车的初始化方法如下：
``` javascript
mounted:function(){
	var _this = this;
	this.$nextTick(function(){
		axios.get('http://localhost:8080/data/cartData.json')
		.then(
		// 使用箭头函数的话，this的作用域并不会变，还是指向vmCart
		// 	(retObj)=>{
		// 	if(retObj.status == 200){
		// 		this.totalMoney = retObj.data.result.totalMoney;
		// 		this.list = retObj.data.result.list;
		// 	}
		// }
		// 不使用箭头函数的场合
		// 如果要使用vmCart实例，
		// 就必须在mounted钩子函数里再调用一次$nextTick方法才能保证el被render在dom中
		function(retObj){
			if(retObj.status == 200){
				vmCart.list = retObj.data.result.list;
			}
		}
		)
		.catch(function(errorObj){
			console.log("get data error...");
		})
	});
},
```
购物车的json数据如下： 
``` json
{
  "productId":"600100002115",
  "productName":"黄鹤楼香烟",
  "productPrice":19,
  "productQuentity":1,
  "productImage":"img/goods-1.jpg",
  "parts":[
    {
      "partsId":"10001",
      "partsName":"打火机",
      "img":"img/part-1.jpg"
    }
  ]
}
```
可以看到，从服务器返回的数据，都是业务逻辑数据(虽然此处是mock的)，并不会包含一些页面上的状态信息，那么如何通过为购物车中的商品增加一些属性来记录诸如是否选中的状态呢？
就需要用到`Vue.set`方法了，详见[官网](https://cn.vuejs.org/v2/api/#vm-set)
``` javascript
methods:{
	selectGood:function(goodObj,index){
		if(goodObj.isChecked == void 0){
			this.$set(goodObj,"isChecked",true)
		} else {
			goodObj.isChecked = !goodObj.isChecked;
		}
		this.isCheckAll();
	}
}
```
如上述代码所示，如果产品被选中，同时该产品没有"isChecked"属性，则为其添加该属性，并设为"true"
至于选中的所有产品的价格，可以使用`computed`属性来计算:
``` javascript
computed:{
	totalPrice:function(){
		var total = 0;
		this.list.forEach(function(good){
			if(good.isChecked){
				total += good.productPrice * good.productQuentity;
			}
		});
		return total;
	},
},
```
最后，可以使用`filters`来为页面上的金额添加单位：
``` javascript
filters:{
	Currency:function(val){
		return val + " 元";
	},
}
```
# 地址
地址页面的大部分功能和购物车类似
只有一个默认地址的设置，可以采用watch的方法来监控当前默认地址的变化，发现变化之后，把老的默认地址取消即可，
这样就不需要遍历所有的地址，将非当前选中的地址都设为“非默认地址”了
``` javascript
watch:{
	currentDefaultAddress:function(newVal,oldVal){
		oldVal.isDefault = false;
	},
}
```
还有一个小技巧是"more"更多地址的设置,目前是通过计算属性来过滤数组的方式:
``` javascript
computed:{
	filteAddress:function(){
		return this.addressList.slice(0,this.limitNum);
	},
}
```
默认是指返回指定数量地址，在按了“more”之后，再将`limitNum`设置为数组的长度，这样就可以显示所有的地址了：
``` html
<div class="shipping-addr-more">
    <a class="addr-more-btn up-down-btn" href="javascript:;" @click="limitNum=(limitNum == addressList.length?3:addressList.length)">
        more
        <i class="i-up-down">
            <i class="i-up-down-l"></i>
            <i class="i-up-down-r"></i>
        </i>
    </a>
</div>
```

# 源码
[github](https://github.com/liu-zhuang/Vue-Demo)

# demo
[url](http://liuzhuang.tech/vue/vue.html)