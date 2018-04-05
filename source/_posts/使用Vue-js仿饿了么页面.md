---
title: 使用Vue.js仿饿了么页面
date: 2017-02-19 19:05:28
tags: Vue.js 
---

# 前言
使用Vue.js脚手架来一步步构建一个仿饿了么的单页面应用(SPA)。
<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="//music.163.com/outchain/player?type=2&id=25706282&auto=1&height=66"></iframe>

# 初始化项目
[参考文件](https://github.com/vuejs/vue-cli)
第一步,安装Vue.js的脚手架工具Vue-Cli
``` 
$ npm install -g vue-cli
```
第二步，使用webpack模板来初始化项目
```
$ npm init webpack Vue-Eleme
```
注：现在的cli版本为2.8.1
然后会提示你输入项目名：Project name,
项目描述：Project description,
作者：Author,
构建方式：Vue build ,通过↑和↓来选择，默认选择Runtime +Compiler,
是否安装vue-router:是否安装路由库,输入y,表示安装
是否使用ESLint来监管你的代码:Use ESlint to lint your code? 为了代码的规范，这里输入y表示“是”,
然后就会让你选择Eslint的预设值，默认使用标准的Standard。按回车继续
是否使用karma和mocha来进行单元测试：Setup unit tests with Karma + Mocha,输入n
是否使用Nightwatch来进行e2e(end to end)的测试：Setup e2e test with Nightwatch,输入n

这时候就会提示你，项目已经构建好了，
同时还会温馨的提示你，如何启动这个项目：
``` 
vue-cli · Generated "Vue-Eleme".

To get started:

cd Vue-Eleme
npm install
npm run dev

Documentation can be found at https://vuejs-templates.github.io/webpack
```
# 项目目录设计
在`src`目录下，分别新建"fonts"、"js"和"less"文件夹，用来存放字体文件，通用js文件和同样样式文件

# Mock数据
在文件夹的根目录增加"data.json"文件，json数据中分别保护"seller"、"goods"和"ratings"三个对象，每个对象都是一个数组。
使用`express`的路由功能来获取这个mock数据  
在"dev-server.js"文件中，找到“var app = express()”，在它下面写以下代码：
``` javascript
// 获取Mock数据
var appMockData = require('../data.json');
var sellerData = appMockData.seller;
var goodsData = appMockData.goods;
var ratingsData = appMockData.ratings;
// 定义路由
var apiRoutes = express.Router();
// 定义访问seller时返回的数据
apiRoutes.get('/seller',function(req,res){
res.json({
errno:0,
data:sellerData
});
});
// 定义访问goods时返回的数据
apiRoutes.get('/goods',function(req,res){
res.json({
errno:0,
data:goodsData
});
});
// 定义访问ratings路由时的数据
apiRoutes.get('/ratings',function(req,res){
res.json({
errno:0,
data:ratingsData
});
});
// 使用这个路由  路径为api/goods 或api/seller api/ratings
app.use('/api',apiRoutes);
```
完成上面代码之后，再重新执行`npm run dev`,就可以通过路由的方式查看mock数据了
在浏览器中分别输入'http://localhost:8080/api/seller','http://localhost:8080/api/goods'和'http://localhost:8080/api/ratings'即可看到对应的数据

# 基础准备工作
首先从[cssreset](http://cssreset.com)下载resetcss样式表，放在static文件夹下的css目录中。
resetcss的作用不用赘述了，使各个浏览器的基础样式保持一致。
接下来在入口文件"index.html"的`head`部分以`link`的形式将`reset.css`引入进来。
由于这个是个移动端的项目，因此，还需要在`head`中增加`viewport`的设置：
``` 
<meta name="viewport" content="width=device-width,initial-scale=1.0,maximum-scale=1.0,minimum-scale=1.0,user-scalable=no">
<link rel="stylesheet" href="static/css/reset.css">
```
宽度等于设备宽度，初始化缩放为100%,最大／最小缩放比例均为100%,不允许用户缩放

在`.eslintrc.js`文件中的`rules`对象中，增加以下几行行：
```
'semi': ['error','always'],// 强制使用分号，否则报错
'indent':0,// 不check 缩进
'no-tabs':0,// 不check no-tabs也就是允许使用tab缩进
```
因为默认的ESlint是要求不使用分号的，但是还是更喜欢习惯使用分号。
接下来编译会报错，根据提示，去把所有地方都加上分号。
下面两个缩进相关的，由于使用Sublime自带的自动缩进，而且已经养成习惯了，因此也就先这样配置了。
（其实是修改Sublime的配置的，不过暂时懒得弄）

# 组件拆分
先看下效果图
![商品页](外卖01_商品页.jpg)
如图所示，商品页由三个部分组成，页头，tabs和内容详情
页头是通用部分，因此可以抽象出来作为组件

## 页面头部
在"components"目录下增加"header"文件夹，并增加"header.vue"文件，作为页头组件
``` vue
<template>
	<div>i'm header</div>
</template>

<script>
	export default {
		name: 'header'
	};
	// ES6 Module
</script>

<style>
	
</style>

```
其中export default是ES6的语法，就是ES6中的Module用法，default关键字用于指定模块的默认输出，详情见阮一峰的ES6教程中的[Module语法](https://github.com/ruanyf/es6tutorial/blob/gh-pages/docs/module.md)

接下来就可以在App.vue中引入`header`组件了，
``` vue
export default {
name: 'app',
components: { 'v-header': header }
};

import header from './components/header/header';
```
通过import的方式引入header,再在export中的`components`注册header即可使用该组件了。
但是有一个地方需要注意的是，如果不使用别名，使用原始的`header`在页面中使用的话就是`<header></header>`，这样的话就会和原生的html标签冲突，因此，在这里需要给它起个别名："v-header",在上面使用的时候也是使用`<v-header></v-header>`.

## 路由
由于是单页面应用，因此使用路由来切换显示内容。
html:
``` html
<section>
	<div class="tab">
		<router-link tag="div" to="/good" class="tabItem">
			<a>商品</a>
		</router-link>
		<router-link tag="div" to="/ratings" class="tabItem">
			<a>评论</a>
		</router-link>
		<router-link tag="div" to="/seller" class="tabItem">
			<a>商家</a>
		</router-link>
	</div>
</section>
<section>
	<router-view></router-view>
</section>
```
样式文件：
``` style
#app .tab{
display:flex;
width:100%;
height: 40px;
line-height: 40px;
}
#app .tab .tabItem{
flex:1;
text-align: center;
}
```
使用flex布局来使3个tab页均等分。
参照router-vue[官网](http://router.vuejs.org/zh-cn/essentials/getting-started.html)的说明，设置路由。
分别将商品，评论和商家设置为三个组件，放置于"components"文件夹下，再在"router"文件夹下的"index.js"中做以下修改：
``` javascript
import Vue from 'vue';
import Router from 'vue-router';
import good from '../components/good/good';
import ratings from '../components/ratings/ratings';
import seller from '../components/seller/seller';
Vue.use(Router);

export default new Router({
routes: [
{ path: '/good', component: good },
{ path: '/ratings', component: ratings },
{ path: '/seller', component: seller }
]
});
```
上述代码的功能就是设置了路由路径和组件之间的映射关系。同时启用了路由。

根据[官方文档](http://router.vuejs.org/zh-cn/essentials/navigation.html)，调用`router.push(path)`方法，在页面加载时，跳转到第一个tab页:
``` javascript
router.push('/good');
```

另外，在router被选中时，会自动增加`router-link-active`样式，该样式会增加在`a`标签上，如果想让该样式作用在其父级元素上，则可以使用以下方法：
``` html
<router-link tag="div" to="/good" class="tabItem">
	<a>商品</a>
</router-link>
```
即将`<router-link`上提。
最后，为了使点击整个tab区域都是有效点击范围，还需要给a标签样式`display:block`。这样整个tab区域都可以点击切换路由了。

## axios vs Vue-resource
在vue2.0点官方文档中，已经不再使用Vue-resource，而开始使用axios来进行进行发起promise式的ajax请求了。
但是这里有个小坑，在使用webpack的时候，和直接引入还是有点区别的，如果直接使用以下命令：
``` 
$ npm install axios --save-dev
```
虽然编译可以通过，但是在打开页面的时候会报错：
"Uncaught (in promise) TypeError: Cannot read property 'protocol' of undefine",
最终在axios的github[issues](https://github.com/mzabriskie/axios/issues/118)中找到了解决方案，
说是在Vue中使用axios时，要再在外面包个壳[vue-axios](https://github.com/imcvampire/vue-axios)
于是最终的解决方案是在`index.js`中写入如下代码：
``` javascript
import axios from 'axios';
import VueAxios from 'vue-axios';
Vue.use(VueAxios, axios);
```

## 将数据传递至组件
在app.vue的添加钩子函数`mounted`来将通过axios获取到的数据传递给`header.vue`组件：
``` javascript
data: function () {
  return {
    seller: {}
  };
},
mounted: function () {
  this.$nextTick(function () {
    this.axios.get('/api/seller')
    .then(res => {
      if (res.data.errno === 0) {
        this.seller = res.data.data;
      }
    })
    .catch(function () {
      console.log('axios error...');
    });
  });
}
```
这样，就可以将`seller`数据通过`props`传递给`header`组件了:
``` html
<v-header :seller="seller"></v-header>
```
在`header.vue`组件中定义"seller":
``` javascript
props: ['seller'],
```
这样就可以通过"seller"来使用数据了。

## stick footer布局
在弹出层显示关闭按钮时，最下面的“X”需要以`stick footer`的布局来展示，否则如果仅仅是使用`postion:fixed`的话，如果上面文字过长，会盖住关闭按钮的。
经典的`stick footer`布局方法见[CSS Secrets](http://www.w3cplus.com/css3/css-secrets/sticky-footers.html)所述。
这里我们采用`padding-bottom`的方式来实现固定底部
``` html
<div v-show="detailShow" class="detail">
	<div class="detail-wrapper clearfix">
		<div class="detail-content">
		</div>
	</div>
	<div class="detail-footer">
		<i class="icon-close"></i>
	</div>
</div>
<style>
.detail {
	position: fixed;
	z-index: 100;
	height: 100%;
	width: 100%;
	left:0;
	top:0;
	background: rgba(7,17,27,0.8);
	overflow: auto;
}

.detail-wrapper {
	min-height: 100%;
	overflow: auto;
}

.detail-content {
	margin-top: 64px;
	padding-bottom:64px;
}
.detail-footer {
	position: relative;
	width: 32px;
	height: 32px;
	margin: -64px auto 0 auto;
	clear:both;
	font-size: 32px;
}
</style>
```

## 过渡效果
在切换商家详细信息显示／隐藏时，如果不加过渡效果会显得略微生硬，这时就要用到Vue的过渡效果了
详见[中文官网](https://cn.vuejs.org/v2/guide/transitions.html)
这里我们就用下最简单的`transition`效果：
``` html
<transition name="fade">
    <p v-if="show">hello</p>
 </transition>
 .fade-enter-active, .fade-leave-active {
   transition: opacity .5s
 }
 .fade-enter, .fade-leave-active {
   opacity: 0
 }
```
即在`v-if`或者`v-show`的区域外再包裹一层`transition`,同时增加相对应的样式即可实现。

## 使用星星来展示商家分数
考虑到有几处地方使用✨来表示商家评分，因此将这个封装为组件
``` vue
<template>
	<div class="ratings">
		<ul  :class="starType">
			<li v-for="star in starList" class="starCommon starItem" :class="star">
			</li>
		</ul>
	</div>
</template>
<script>
	const LENGTH = 5;
	const CLS_ON = 'on';
	const CLS_HALF = 'half';
	const CLS_OFF = 'off';
	export default {
		props: {
			starSize: Number,
			score: Number
		},
		computed: {
			starType: function () {
				return 'starSize-' + this.starSize;
			},
			starList: function () {
				// 通过向下取整的方式计算一共有几颗星
				var starCnt = Math.floor(this.score);
				var result = [];
				// 有几颗星加几个'on'
				for (let i = 0; i < starCnt; i++) {
					result.push(CLS_ON);
				}
				// 分数减去星数获取剩余小数点后面的分数
				var leftVal = this.score - starCnt;
				// 如果大于等于0.5增增加一个半星
				if (leftVal >= 0.5) {
					result.push(CLS_HALF);
				}
				// 如果结果数组的长度小于5，则补空星
				while (result.length < LENGTH) {
					result.push(CLS_OFF);
				}
				return result;
			}
		}
	};
</script>
<style type="text/css" lang="less">
	.ratings {
		.starCommon {
			display: inline-block;
			background-repeat: no-repeat;
			-moz-background-size:100% 100%;
			background-size:100% 100%;
			-webkit-background-size:100% 100%;
		}
		.starSize-24 {			
			.starItem {
				width:10px;
				height: 10px;
				margin-right: 3px;	
			}
			.starItem:last-child {
				margin-right: 0px;
			}	
			.on {
				background-image: url('star24_on@2x.png');
			}
			.half {
				background-image: url('star24_half@2x.png');
			}
			.off {
				background-image: url('star24_off@2x.png');
			}
		}
		.starSize-36 {
			.starItem {
				width:15px;
				height: 15px;
				margin-right: 6px;	
			}
			.starItem:last-child {
				margin-right: 0px;
			}	
			.on {
				background-image: url('star36_on@2x.png');
			}
			.half {
				background-image: url('star36_half@2x.png');
			}
			.off {
				background-image: url('star36_off@2x.png');
			}
		}
		.starSize-48 {
			.starItem {
				width:20px;
				height: 20px;
				margin-right: 22px;	
			}
			.starItem:last-child {
				margin-right: 0px;
			}	
			.on {
				background-image: url('star48_on@2x.png');
			}
			.half {
				background-image: url('star48_half@2x.png');
			}
			.off {
				background-image: url('star48_off@2x.png');
			}
		}
	}
</style>
```

## backdrop-filter
在给弹出的商家详情页添加模糊背景时，发现如果增加了`filter(blur10px)`样式，则除了背景色外，所有的文字也都模糊了，因此需要使用`backdrop-filter`这个样式来单单为背景色添加模糊效果。关于`backdrop-filter`的详情戳[这里](http://www.w3cplus.com/blog/tags/497.html)

## 小结
功能做完了，但是感觉要写的东西好多，没空写了。。
演示地址：[demo](http://liuzhuang.tech/eleme/eleme.html#/good)
源码: [github](https://github.com/liu-zhuang/Eleme)
好心人给大叔来个star呗。。。