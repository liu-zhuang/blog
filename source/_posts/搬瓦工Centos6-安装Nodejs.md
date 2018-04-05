---
title: 搬瓦工Centos6 安装Nodejs
date: 2017-03-13 10:34:53
tags: 搬瓦工 BandwagonHost Centos node.js
---

# 前言
买了个便宜的VPS--搬瓦工玩玩，只能装开源的OS，因此选了Centos 6。
想再配个Node.js一起玩玩，于是开始上网查了。看到很多人都说安装编译版会遇到很多各种各样的坑，懒得折腾，于是找了最简单的二进制安装方法。

# 下载
进入Nodejs[下载主页](https://nodejs.org/en/download/)
找到Linux Binaries (x86/x64)，它的右边就是32和64位的下载地址，找到自己os对应位数的链接，右键复制地址。
我这里获取到的是：https://nodejs.org/dist/v6.10.0/node-v6.10.0-linux-x64.tar.xz
然后在命令行输入：
```
wget https://nodejs.org/dist/v6.10.0/node-v6.10.0-linux-x64.tar.xz 
```
即可下载至当前目录

# 解压
由于下载下来的是.tar.xz文件，因此要进行两步解压
使用xz -d xxx.tar.xz 将 xxx.tar.xz解压成 xxx.tar
再用tar -xvf xxx.tar解压:
```
xz -d node-v6.10.0-linux-x64.tar.xz
tar -xvf node-v6.10.0-linux-x64.tar
```

# 移动目录
使用以下命令将其移至`opt`目录下，并改名为`node`:
```
mv node-v6.10.0-linux-x64 /opt/node
```

# 全局变量链接
如果不在全局变量中进行配置，`node`命令将只能在`opt/node/bin`下使用，因此需要使用以下命令将`node/bin`和全局变量链接起来：
```
ln -s /opt/node/bin/node /usr/local/bin/node
ln -s /opt/node/bin/npm /usr/local/bin/npm
```

# 结语
至此，Centos 6下的Nodejs安装就完成了。
可以在任意目录下使用`node -v`来测试是否安装/配置成功。