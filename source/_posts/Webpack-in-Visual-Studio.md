---
title: Webpack in Visual Studio
date: 2017-06-07 17:09:50
tags: webpack vs visual stuio
---

# 前言
用webpack来构建纯前端项目已经司空见惯了，但是，其实并没有很多公司真正的实现前后端分离，所以使用asp.net mvc开发还是很普遍的。
之前使用.net mvc + vue + elementui 开发感觉得心应手，效率高了很多，但是遇到一个问题：在使用ES6语法时，会遇到兼容性问题。
刚开始的时候采用了`traceur-compiler`来进行实时编译，后来觉得这个总不是best practise，于是开始尝试使用自动构建的方式来实现。

# Gulp
最初的打算是使用Gulp来进行自动构建，但是发现使用Gulp有以下几个问题：
1. 需要依赖的库太多
2. 想实现诸如增加基于内容的hash值作为文件名称和实现"livereload"都比较麻烦



# Webpack
综合考虑下还是决定直接使用webpack来做：
1. 直接可以输出hashchunkname
2. 简单的在命令行加一个`-w`就可以实现"livereload"


## 基本应用
引入webpack的最基本目的是为了编译ES6的代码，因此只需要引入`babel`就可以了。

- 项目文件的根目录执行`npm init`来初始化`package.json`文件（一路回车即可)
- 在当前项目目录下执行`npm install --save-dev webpack babel-core babel-loader babel-preset-2015`，这样最基础的插件就安装好了
- 接下来还要安装两个插件，第一个是`clean-webpack-plugin`，用于清除目录，第二个是`assets-webpack-plugin`,这个用以生成将js文件和带chunkhash值的文件映射关系的文件，最后还有一个用以装逼的插件`webpack-notifier`,这个是用来通知webpack执行状态的 (注意：这里如果需要使用`babel-polyfill`也可以引入)


这时没什么好多说的，都是webpack的基本使用，设置entry,module里面设置使用`babel-loader`来加载".js"文件
同时在执行webpack脚本时增加'-w'参数,就可以实现修改js文件后自动编译了

## 长效缓存
为了实现长效缓存，[戳这里](https://github.com/fouber/blog/issues/6),还需要使用` '[name].[chunkhash].js'`来给编译后的js名添加hash值，但是这样就产生了很多新的问题
- 因为hash值是根据内容动态变化的，那么开发的时候怎么知道该引用什么名字呢？
- 同样因为是动态变化的，所以不能把这些文件包含在项目中，因此导致发布的时候，这些文件并不会复制到发布目录
解决办法如下：
1. 使用上面提到的`assets-webpack-plugin`插件，可以在构建时，生成filename与filename-chunkhash的映射关系。并将这个映射关系保存下来,这样就可以读取这个文件，以类似反射的方式加载
2. 为了这个问题还去研究了一下msbuild，最终通过自己手动复制的方式实现

## 自动反射业务js文件
考虑到如果需要每次新增一个js文件，都要去修改webpack.config.js的入口(`entry`)未免有点麻烦,于是考虑也用类似反射的思路，来读取用来存放业务js代码的文件夹，把每个".js"都作为入口文件的一个对象:
``` javascript
var readFile = function (path) {
    var fs = require('fs');
    var files = fs.readdirSync('./' + path);
    var jsFiles = files.filter((f) => {
        return f.endsWith('.js');
    });

    var ret = {};
    jsFiles.forEach(function (item) {
        var temp = item.substring(item, item.indexOf(".js"));
        ret[temp] = "./" + SOURCE_DIRECTORY + "/" + item;

    });
    return ret;
};

```
最终生成出来的样子如下所示：
```
// entry: {
//   wp:[ './app/webpack.js'] // 独立的业务文件

// }, // 入口文件定义
```


## 举个栗子

最终的`webpack.config.js`文件应该长这样：
``` javascript
'use strict';

const AssetsPlugin = require('assets-webpack-plugin'); // 将每个资源文件的原生filename和chunkhashname一一对应起来的插件
const CleanPlugin = require('clean-webpack-plugin'); // 清空目录的插件
const path = require('path'); // 顾名思义，路径相关操作的插件
const webpack = require('webpack'); // 主角，webpack
const pkg = require('./package'); // package.json
var WebpackNotifierPlugin = require('webpack-notifier');

const SCRIPTS_ROOT = 'Content/Scripts/';
const SOURCE_DIRECTORY = 'app'; // 业务文件名
const BUILD_DIRECTORY = 'build'; // 构建目录名
const BUILD_DROP_PATH = path.join(__dirname, SCRIPTS_ROOT, BUILD_DIRECTORY);
//const BUILD_DROP_PATH = path.resolve(__dirname, BUILD_DIRECTORY); // 构建全路径
const WEB_ROOT = path.join(__dirname, SCRIPTS_ROOT); // 当前上下文


const CHUNK_FILE_NAME = '[name].[chunkhash].js';

var readFile = function (path) {
    var fs = require('fs');
    var files = fs.readdirSync('./' + path);
    var jsFiles = files.filter((f) => {
        return f.endsWith('.js');
    });

    var ret = {};
    jsFiles.forEach(function (item) {
        var temp = item.substring(item, item.indexOf(".js"));
        ret[temp] = "./" + SOURCE_DIRECTORY + "/" + item;

    });
    return ret;
};


var webpackEntry = readFile(path.join(SCRIPTS_ROOT, SOURCE_DIRECTORY));

console.log(webpackEntry);

const config = {

    context: WEB_ROOT, // 声明上下文

    // entry: {
    //   wp:[ './app/webpack.js'] // 独立的业务文件

    // }, // 入口文件定义
    entry: webpackEntry,

    module: {
        loaders: [
        {
            test: /\.js$/,
            loader: 'babel-loader',
            query: {
                presets: ['es2015']
            },
            exclude: /node_modules/
        },
        {
            test: /(\.css$)/,
            loaders: ['style-loader', 'css-loader', 'postcss-loader']
        },
       {
           test: /\.(png|woff|woff2|eot|ttf|svg)$/,
           loader: 'url-loader?limit=100000'
       }
        ]
    }, // 使用babel编译js


    output: {
        path: BUILD_DROP_PATH,
        filename: CHUNK_FILE_NAME,
        chunkFilename: CHUNK_FILE_NAME
    }, // 输出

    plugins: [
    new AssetsPlugin({
        filename: 'webpack.assets.json',
        path: BUILD_DROP_PATH,
        prettyPrint: true
    }), // 将filename 和 filename.chunkhash对应起来

    new CleanPlugin(BUILD_DROP_PATH), // 构建前先清空文件夹

    new webpack.optimize.UglifyJsPlugin({
        compress: {
            warnings: false
        },
        output: {
            comments: false
        }
    }), // 压缩混淆

    new WebpackNotifierPlugin(), // 通知结果
    ],
};

module.exports = config;
```

到目前为止，webpack能做的事就结束了，接下来.net怎么配合呢？

## cshtml页面引用

前面提到了，编译出来的js文件名是含有chunkhash值的，于是最理想的使用方法就是在开发过程中感觉不到变化，还是像之前一样，只需要写"filename"就可以引入含有"chunkhash值"的编译后的js文件。
做法如下：
首先，扩展一下UrlHelper：
```
    public static class WebpackExtension
    {
        public static string GetResourceUrlHashChunk(this UrlHelper helper, string fileName)
        {
            JObject webpackAssetsJson = null;
            var applicationBasePath = System.AppDomain.CurrentDomain.BaseDirectory;
            string packageJsonFilePath = $"{applicationBasePath}\\{"package.json"}";

            using (StreamReader packageJsonFile = File.OpenText(packageJsonFilePath))
            {
                using (JsonTextReader packageJsonReader = new JsonTextReader(packageJsonFile))
                {
                    JObject packageJson = (JObject)JToken.ReadFrom(packageJsonReader);
                    JObject webpackConfigJson = (JObject)packageJson["customConfig"]["webpackConfig"];
                    string webpackAssetsFileName = webpackConfigJson["assetsFileName"].Value<string>();
                    string webpackBuildDirectory = webpackConfigJson["buildDirectory"].Value<string>();
                    string webpackAssetsFilePath = $"{applicationBasePath}\\{webpackBuildDirectory}\\{webpackAssetsFileName}";

                    using (StreamReader webpackAssetsFile = File.OpenText(webpackAssetsFilePath))
                    {
                        using (JsonTextReader webpackAssetsReader = new JsonTextReader(webpackAssetsFile))
                        {
                            webpackAssetsJson = (JObject)JToken.ReadFrom(webpackAssetsReader);
                        }
                    }
                    return "~/" + webpackBuildDirectory + webpackAssetsJson.SelectToken(fileName).Value<string>("js");

                }
            }
        }
    }
```
这段代码做的事情就是把`AssetsPlugin`这个插件生成的包含映射关系的文件读出来，然后根据文件名找到真实路径
于是在前台(cshtml)页面中使用时，可以直接使用以下方法：
```
 <script type="text/javascript" src="@Url.Content(@Url.GetResourceUrlHashChunk("webpack"))"></script>
 ```
 注：webpack是我这个js的名称

 ## 发布问题
 事情到这里还没有结束，在发布时又发生了状况，那就是这些编译后的文件由于是动态生成的，无法包入项目文件，因此也就不会复制的发布目录中，解决方法如下：

 在项目根目录下的`Properties\PublishProfiles`文件夹中，会保存发布的配置文件，由于visual studio是使用MSBuild进行编译发布的，因此在对MSbuild略加研究之后，`pubxml`的内容如下：
 ``` 
<?xml version="1.0" encoding="utf-8"?>
<!--
您 Web 项目的发布/打包进程将使用此文件。您可以通过编辑此 MSBuild 文件
来自定义该进程的行为。若要了解与此相关的详细信息，请访问 http://go.microsoft.com/fwlink/?LinkID=208121。
-->
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <WebPublishMethod>FileSystem</WebPublishMethod>
    <LastUsedBuildConfiguration>Release</LastUsedBuildConfiguration>
    <LastUsedPlatform>Any CPU</LastUsedPlatform>
    <SiteUrlToLaunchAfterPublish />
    <LaunchSiteAfterPublish>True</LaunchSiteAfterPublish>
    <ExcludeApp_Data>False</ExcludeApp_Data>
    <publishUrl>F:\WebDemo\webpack</publishUrl>
    <jsPublishUrl>F:\WebDemo\webpack\Content\Scripts\build</jsPublishUrl>
    <!--这里务必设为false-->
    <DeleteExistingFiles>False</DeleteExistingFiles>
    <PrecompileBeforePublish>True</PrecompileBeforePublish>
    <EnableUpdateable>True</EnableUpdateable>
    <DebugSymbols>False</DebugSymbols>
    <WDPMergeOption>DonotMerge</WDPMergeOption>
  </PropertyGroup>
  <ItemGroup>
    <!--定义需要copy到发布目录的文件
    定义发布前需要先删除的文件夹-->
    <WebFiles Include="$(ProjectDir)\Content\Scripts\build\*.*" />
    <OutputFiles Include="$(publishUrl)\**\*.*" />
  </ItemGroup>
  <!--先删除之前老的发布文件-->
  <Target Name="RemoveDirectories" AfterTargets="AfterBuild">
    <Delete Files="@(OutputFiles)" />
  </Target>
  <!--将带hashchunk的压缩过的js文件copy到发布目录-->
  <Target Name="moveChunkhashJs" AfterTargets="RemoveDirectories">
    <MakeDir Directories="$(jsPublishUrl)" Condition="!Exists('$(jsPublishUrl)')" />
    <Copy SourceFiles="@(WebFiles)" DestinationFolder="$(jsPublishUrl)">
    </Copy>
  </Target>
</Project>

 ```


 ## vs2015 webpack task runner 插件
 在vs2015里还可以直接安装webpack task runner 插件来使用
 如图所示，还可以将webpack脚本和生成事件绑定起来了。
 最后还有一个小坑：[戳这里](https://github.com/madskristensen/NpmTaskRunner/issues/47)
 如果你在命令行运行脚本没问题，但是使用webpack task runner 出了问题，很有可能就是这个原因，配置"外部Web公交"时，的位置顺序不对。
 ![plugin1](webpack task runner.png)
 ![plugin2](webpack task runner2.png)
 ![plugin3](webpack task runner3.png)


 # 小结

 其实还有很多可以优化的地方，比如第三方库，css等，但是目前已经可以满足初步需求了。接下来再做进一步的优化。



 # 栗子
 [栗子](https://github.com/liu-zhuang/webpack-vs/)


 