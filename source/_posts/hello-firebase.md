---
title: hello firebase
date: 2017-04-17 22:21:56
tags: firebase realtime database google
---

# 前言
由于多说即将关闭，要给博客找个新的评论工具了，没有找到特别喜欢的，所以打算自己做一个算了。
![duoshuo](duoshuo.png)
于是，想到了为数不多的被孤🐩(google)收购之后没有改名FireBase（魂淡，虽然名字没有改，但是域名改成google了啊，你懂的），去年还没有自己搭VPS科学上网，蓝灯侠出专业版之后也极其不给力，所以虽然关注过一段时间之后就淡忘了。今年，呵呵，我有梯子我怕谁？

# 实战
关于firebase的介绍就不多说了，文案写的也是没谁了，开发一个app只需要30秒之类的，不过确实30分钟可以搞个聊天室出来了。
跟着官方文档一路下一步完成注册，就来到到Console首页，会问你是想将Firebase应用在什么地方：1.ios 2.android 3.web app。选择web app后将会把使用firebase所需要写的代码的30%贴给你(ノへ￣、)捂脸
![firebase](firebase.png)
当然了，如果是使用module化JavaScript，不能这样写。不过也差不大多。

接下来利用上图给出的config实例化database:
![init](firebase1.png)
这里用到了vuefire.js（在vue实例中增加了firebase属性,用法和vue原生的data,props一样)
然后也有CRUD这些方法:
![method](firebase2.png)
这样就可以实现基本功能了。

# realtime database
来看张动图感受下毫秒级的实时数据库同步吧
![gif](realtime database2.gif)

# 后记
猜测firebase是使用Websocket来实现realtime database效果的？
正如前面所说，使用Vue + FireBase 仅用30分钟就可以搭建一个聊天室。
打算接下来有空搭建一个玩玩。

