---
title: Vue-Music (2)
date: 2017-07-15 16:04:25
tags: vue vue-cli vue-music
---
# 基本骨架
首先需要安装使用`npm install --save-dev babel-runtime fastclick babel-polyfill`安装这三个组件
babel两个就不用多说了，fastclick是为了避免移动设备上点击的300毫秒延迟而导入的组件

然后在index.html页面的head中增加一个meta:
```
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0, user-scalable=no">
```
移动端开发必备的内容，也不需要赘述了

# header组件 
首先，在"common/less"目录下增加`mixin.less`文件。
里面先增加一个".bg-image"的定义（在LESS里面，这个叫[混合](http://www.bootcss.com/p/lesscss/))
```
// 背景图片
.bg-image(@url) {
	background-image: ~"url(@{url}@2x.png)";
	 @media (-webkit-min-device-pixel-ratio: 3),(min-device-pixel-ratio: 3) {
	 	background-image: ~"url(@{url}@3x.png)";	
	 }
}
```
它的作用就是使用`@media`判断当设备像素比是3的时候，采用"3x"的图片
这里有两个注意点:
- less的字符串拼接不能用`+`，只能用上文中这种类似js模版的方法
- 路径前一定要加`~`,否则返回的是该".less"文件所在的路径，而不是使用该方法的文件所在路径

header组件中暂时没有什么需要交互的逻辑，就是一个简单的logo和一个标题
```
<template>
	<div class="m-header">
		<div class="icon"></div>
		<h1 class="title">Vue Music</h1>
	</div>
</template>
<script>
	export default {
		name: 'm-header'
	};
</script>
<style scoped lang="less">
	@import '~less/variable.less';
	@import '~less/mixin.less';
	.m-header {
		display: flex;
		flex-direction:row;
		justify-content:center;
		align-items:center;
		position: relative;
		height: 44px;
		font-size: 0;
		color: @color-theme;
		.icon {
			display: inline-block;
			width:30px;
			height: 32px;
			.bg-image('logo');
			background-size: 30px 32px;
			margin-right:10px;
		}
		.title {
			display: inline-block;
			font-size: @font-size-large;
		}
	}
</style>
```
然后在`app.vue`中import该组件就可以使用了

# tab组件
tab组件用来切换路由
这里简单的使用`<router-link>`来实现路由切换。
具体使用见[官方文档](https://router.vuejs.org/zh-cn/api/router-link.html)

这里有个小tips：
如果希望在页面初始化的时候，就默认选择某个tab，可以在路由的设置中增加：
```
	{
		path: '/',
		redirect: '/recommend' // 默认选中  注意这里是redirect 值是路径 同时注意区分大小写，路由是否选中是根据url路径来判断的
	}
```
需要注意的是，这个`redirect`是区分大小写的。路由是否选中是通过url中的hash值来决定的，如果大小写不一致，tab就不会有选中状态。

[代码](https://github.com/liu-zhuang/vue-music/tree/路由+顶部导航)
