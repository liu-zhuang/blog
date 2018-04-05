---
title: Vue Music (1)
date: 2017-07-15 13:11:47
tags: vue vue-cli vue-music 
---
# 前言
继仿造饿了么之后的使用Vue全家桶的高级练习

# 初始化项目框架
既然是全家桶，初始化没什么好说的，使用webpack框架
```
vue init webpack vue-music
```
项目代码统统写在`src`目录下，同时在原有目录结构下增加一些目录
├── src
│   ├── App.vue     => 入口视图
│   ├── api         => api
│   ├── common     
│   │   ├── fonts   => 字体
│   │   ├── image   => 图片
│   │   ├── js	    => js库
│   │   └── less    => 样式文件
│   ├── components  => 组件
│   ├── main.js     => 入口js
│   ├── router      => 路由
│   └── store       => vuex状态管理

## tips
mac os下生成上面这种目录结构方法如下：
- 使用`brew install tree`安装tree
- 使用`tree -L 2`查看2个层级的树形目录结构（当然数字可以任意)
- 也可以使用`tree -L 2 >Tree.md` 来将目录结构直接导出

## 样式
由于vue-cli已经在`build\utils`中为less,stylus,sass进行了预设，因此可以无障碍直接使用less,只需要在style标签上写明`lang='less'`以及`rel="stylesheet/less"`即可。

## alias
在使用`import`或者`require`引入js或者样式文件的时候，路径过长是个比较麻烦的问题，因此如果使用webpack中的别名(alias)可以提高不少效率。这里需要注意的是有两个坑：
- 在`build\webpack.base.conf.js`中修改别名后，一定要停掉当前服务，重新执行`npm run dev`才会生效
- 在js环境中直接使用别名即可，在style环境中使用别名要在前面加上波浪线`~`
如下所示：
webpack.base.conf.js:
```
alias: {
       'less': resolve('src/common/less'), // 设置less别名
       'com': resolve('src/components') // 设置组件别名
    }
```
main.js:
```
import Vue from 'vue';
import App from './App';
import router from './router';
import 'less/index.less'; // 引入样式文件
```
app.vue:
```
<style scoped lang="less" rel="stylesheet/less">
 @import '~less/variable.less'; // less别名
#app {
  color: @color-theme;
}
</style>
```

至此，项目的基本框架就搭建好了。
[代码地址](https://github.com/liu-zhuang/vue-music/tree/init)
项目的init分支

