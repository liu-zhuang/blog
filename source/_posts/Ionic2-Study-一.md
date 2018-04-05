---
title: Ionic2 Study(一)
date: 2016-11-15 07:28:31
tags: Ionic Ionic2 
---
# 前言
做app的心由来已久了，身为一个大叔程序猿怎么可以不会做app，当年swift刚出的时候，跟着斯坦福大学的白胡子学过几天。。但是没有坚持下去。。这次希望可以坚持久点-。-
app开发现在无外乎三种，native,Hybrid和web app，首先排除掉了web app，因为日常开发虽然没有mobile first，但是响应式已经是需求的一部分的，native也有考虑过，要不要把swift坚持下去，但是考虑到毕竟不是科班出身，半路出家做输在起跑线上了。。
最终还是决定用Hybrid。
另外，其实也有考虑过react native,但是由于react本身就没学过，angular虽然不精通，但至少也算是入门了的，考虑到ionic2用的是ng2，将来对web开发也能有所帮助，所以决定先以Ionic入手。

# 安装环境
Ionic2已经Release了，现在安装已经不需要像测试的时候后面加什么Beta啦，版本号啦之类的。
直接安装官网的说明：
``` 
npm install -g ionic cordova
```
在终端(mac)或者命令行工具(windows)输入以上命令即可
含义就是使用npm，在全局安装ionic和cordova
当然啦，考虑到我大天朝的网络环境，如果不翻墙，以上安装过程基本上是很难完成的，所以我大天朝的苦逼程序猿们就想到了使用镜像的方式来复制npm。
毕竟有名的是淘宝源和cnpm，cnpm似乎刷新的不是很快，而淘宝源据说是10分钟就去npm拉一次。
话说第一次使用淘宝源的时候也不靠谱，都打算放弃在找经济实惠的梯子的时候，突然淘宝源又给力起来了。。。（说明也不稳定？）
[淘宝源地址](https://npm.taobao.org)
一般用户（需要向npm发布的用户除外），最简单的使用方法就是在npmrc文件中增加:
```
registry = http://registry.npm.taobao.org
```
上面这句话即可。mac系统一般默认是没有这个文件的，需要自己手动写touch npmrc先生成这个文件才行
另外，mac系统的路径如下："usr/local/lib/node_modules/npm"
完成上述操作之后，再执行npm install 就会有飞一般的感觉。。。速度快到感动的想哭。。

# 初始化项目
老生常谈，如果你在没有使用淘宝源的情况下（真的不是在做广告），艰难的安装好了Ionic和cordova，那么初始化项目你如果不使用淘宝源绝对会痛不欲生。。
初始化项目使用的命令如下：
```
ionic start demo1 --v2
```
在demo1处替换为你的项目名即可。执行该命令会去github上下载一些模版和众多的npm包。
（ps：在项目名（demo1）后面可以加上预制模版名的，比如：tutorial，如果不加特殊说明，则会使用tabs的模版来初始化项目）
后面加的--v2表示用Ionic2来初始化项目
![项目目录](ionic1.png)
初始化结束之后的目录结构如上图所示。
hooks:顾名思义就是钩子的意思，放的是Cordova的钩子，主要是build用的吧？（我猜），一般情况下不会去改
npm_modules:放的是从npm下载的需要用到的包
platforms:放的是为ios和Android平台生成的开发包
plugins：放的是cordova的一些插件
resources:资源文件，主要是一些图片
src：源码
www：入口文件

# 运行项目
在上面的初始化项目结束之后
在终端输入：
```
cd demo1
ionic serve
```
就会在浏览器运行起来了
![预览](ionic2.png)
