---
title: Vue-Music (3)
date: 2017-07-16 00:33:32
tags: vue vue-cli vue-music
---
# 推荐页面
数据来源都是qq音乐，有的是从[移动端](https://m.y.qq.com/)找api，有的从[pc端](https://y.qq.com/)找。
推荐页面由两部分组成，上面是一个slider,下面是推荐的歌单
slider的数据源是从'https://c.y.qq.com/musichall/fcgi-bin/fcg_yqqhomepagerecommend.fcg'这个接口获取的，为了解决跨域问题，我们format使用jsonp。

## fetch
鉴于各种原因，本次异步请求大部分采用fetch的方式([文档](https://developer.mozilla.org/zh-CN/docs/Web/API/Fetch_API)),同时由于跨域的问题，需要jsonp,使用了[fetch-jsonp](// https://github.com/camsong/fetch-jsonp).这就不多做说明
对fetch-jsonp进行简单的封装，放在'src/common/js/fetch-jsonp.js'中，主要做的事情就是把查询参数以对象的方式传入，再拼接url，最后调用fetch-jsonp方法获取接口数据，并返回promise对象。

## api
将api单独剥离出来，放在'src/api/recommend.js'下。
同时进行简单的封装。
