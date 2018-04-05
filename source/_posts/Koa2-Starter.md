---
title: Koa2 Starter
date: 2017-05-31 11:24:15
tags: koa koa2 node javascript
---

# Koa2入门
本来是想Express入门的，但是既然都是要学，干嘛不学最新的呢？
其实我想说，我本来只是想学个小程序开发，现在已经陆陆续续开了好多坑了。。
本文参考[廖雪峰教程](http://www.liaoxuefeng.com/wiki/001434446689867b27157e896e74d51a89c25cc8b43bdb3000/001471087582981d6c0ea265bf241b59a04fa6f61d767f6000#0)

# Async
最新的Koa2已经使用async来作为中间件(middleware)了，具体用法这里不赘述，可以参考[阮一峰老师的Es6教程](http://es6.ruanyifeng.com/#docs/async)
之前使用的Generator函数

使用async时，要求node的版本大于等于7.6.0
因此如果发现错误，可以使用nvm（window系统），n（mac系统）来切换node版本

mac系统的n已经用的很多了，这里放出window系统下的nvm的一些介绍：
[下载](https://github.com/coreybutler/nvm-windows/releases)
[nvm使用说明](https://segmentfault.com/a/1190000007612011)


# 实例
一. 使用`npm init`命令初始化`package.json`

二. 使用`npm install --save-dev koa koa-bodyparser koa-router`安装koa2
其中koa和koa-router毋庸置疑字面含义已经很清楚了，koa-bodyparser是因为无论是原生的node还是koa都无法直接解析request的body，因此需要引用这个插件

三. 在根目录创建文件夹：`controllers`,在该文件夹中保存所有的路由文件

四. 在`controllers`文件夹中新增一个名为`index.js`的文件，内容如下：
``` javascript
var fn_index = async (ctx, next) => {
	// let appID = 'wx5db51f3c5c381ed2',
	// appsecret = '4c797335101c71a01c1713b3f83fd26d',
	// Token = 'xiaoyebaomemeda';
	
	console.log(ctx.querystring);
	ctx.response.body = `<h1>Index </h1><h3>koa1</h3><h4>${ctx.querystring}</h4>`;
};

var fn_hello = async (ctx, next) => {
	var name = ctx.params.name;
	ctx.response.type = 'text/html';
	ctx.response.body = `<h3>Hello ${name}</h3>`;
};

module.exports = {
	'Get /': fn_index,
	'Get /hello/:name': fn_hello
};	

```
可以看出，这个文件输出的对象中包含两个”路由“，分别是"Get /"和"Get /hello/:name"
"Get"表示method是"get","/"是url路径
关于ctx的介绍详见[官网](http://www.koacn.com/#context)
五. 在`controllers`文件夹再新建一个名为`user.js`的文件,内容如下：
``` javascript
var fn_get_register = async (ctx, next) => {
	var html = `
	<!DOCTYPE html>
	<html>
	<head>
	<title>register</title>
	</head>
	<body>
	<form action="/register" method="post">
	姓名：<input type="text" name='name' value=''>
	年龄：<input type="" name="age">
	<button type="submit">submit</button>
	</form>
	</body>
	</html>		
	`;
	ctx.response.type = 'text/html';
	ctx.response.body = html;
};


var fn_post_register = async (ctx, next) => {
	console.log(`name:${ctx.request.body.name}
		age: ${ctx.request.body.age}`);
	ctx.response.body = `<span>submited!</span>
	name:${ctx.request.body.name}
	age:${ctx.request.body.age}`;
};


module.exports = {
	'Get /register': fn_get_register,
	'Post /register': fn_post_register
};
```
这次分别创建了"Get"和"Post"方法的路由

六. 在根目录创建controller.js,用来读取controller文件夹中的所有路由文件，反射出路由,内容如下：
``` javascript
// 返回path路径下的所有js文件
var readFile = function (path) {
	var fs = require('fs');
	var files = fs.readdirSync('./'+path);
	var jsFiles = files.filter((f) => {
		return f.endsWith('.js');
	});
	return jsFiles;
};

// 根据.js文件反射路由
var SetRouter = function (path) {
	// 声明路由
	var router = require('koa-router')();

	var jsFiles = readFile(path);
	for (var js of jsFiles) {
		let mapping = require(__dirname + '/' + path + '/' + js); //分别引入每个js文件
		for (var url in mapping) {
			// 遍历每个controller文件中的路由
			if(url.startsWith('Get')) {
				// method: get
				router.get(url.replace('Get ',''), mapping[url]);
			} else if (url.startsWith('Post'),mapping[url]) {
				// method: post
				router.post(url.replace('Post ',''), mapping[url]);
			} else {
				// 非路由的
				console.log('this is not router file');
			}
		}
	}
	return router.routes();
};


module.exports = function (path) {
	return SetRouter( path || 'controllers');
};

```

七. 在根目录新建app.js,内容如下：
``` javascript
var Koa = require('koa');
var app = new Koa();
var router = require('./controller.js')();

var sha1 = require('sha1');
var bodyParser = require('koa-bodyparser');

app.use(async (ctx,next) => {
	console.log(new Date());
	await next ();
});


app.use(bodyParser());
app.use(router);



app.listen(3000);
console.log('listening port 3000');
```

八. 在命令行使用命令：`node app.js`即可启动服务


九. 在浏览器输入：`localhost:3000`即可访问


# 源码
[github地址](https://github.com/liu-zhuang/wechat/)
[个人网站](wwww.liuzhuang.tech)