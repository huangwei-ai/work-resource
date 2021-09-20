## 一.plugin有什么用
>plugin是webpack核心功能，通过plugin（插件）webpack可以实现loader所不能完成的复杂功能，使用plugin丰富的自定义API，可以控制webpack编译流程的每个环节，实现对webpack的自定义功能扩展。
### 举例
我们实际项目中就使用了HtmlWebpackPlugin插件，它帮助我们做了下面几件事儿：

- 在工程打包成功后会自动生成一个html模板文件
- 同时所依赖的CSS/JS也都会被自动引入到这个html模板文件中
- 设置生成hash添加在引入文件地址的末尾，类似于我们常用的时间戳，来解决可能会遇到的缓存问题。
项目打包后生成的模板文件如下：
```
<!DOCTYPE html>
<html>
<head>
  <meta charset=utf-8>
  <title>移山</title>
  <link rel=icon href=/static/assets/favicon.ico type=image/x-icon>
  <link href=/static/css/app.37f937e3e08602bbb89778796e294cf1.css rel=stylesheet>
</head>
<body>
<div id=app>
</div>
<script type=text/javascript src=/static/js/manifest.2ae2e69a05c33dfc65f8.js></script>
<script type=text/javascript src=/static/js/vendor.d903c30c8b95cb48653b.js></script>
<script type=text/javascript src=/static/js/app.0c675ae0a3c300e0af57.js></script>
</body>
</html>
```
## 二.什么是plugin
>plugin是一个具有 apply方法的 js对象。 apply方法会被 webpack的 compiler（编译器）对象调用，并且 compiler 对象可在整个 compilation（编译）生命周期内访问。
一个plugin看起来大概是这个样子：
```
function CustomPlugin(options){
  // options是配置文件，你可以在这里进行一些与options相关的工作
}

// 每个plugin都必须定义一个apply方法，webpack会自动调用这个方法
CustomPlugin.prototype.apply = function(compiler){
    ......
    });
}

module.exports = CustomPlugin;
```

## 三.使用plugin
在 webpack 配置文件（webpack.config.js）中，向 plugins 属性传入 new 实例即可。比如：
```
const HtmlWebpackPlugin = require('html-webpack-plugin');
const webpack = require('webpack');
module.exports = {
  
  module: {
    loaders: [
      {
        test: /\.(js|jsx)$/,
        loader: 'babel-loader'
      }
    ]
  },
  plugins: [
    new webpack.optimize.UglifyJsPlugin(), //访问内置的插件
    new HtmlWebpackPlugin({template: './src/index.html'}) //访问第三方插件
  ]
};
```
注意
- webpack中的插件分为内置插件和第三方插件
- 内置插件不需要额外安装依赖，如上面的例子中：UglifyJsPlugin插件
- 如果是第三方插件，如上面的例子中HtmlWebpackPlugin插件，则使用之前需要进行安装：
```
npm install html-webpack-plugin --save-dev
```
## 四.案例
在对plugin有了一个基本认识后，来做一个小案例：

>“我想对所有的文件打包后添加一个版权声明”

目录结构
```
webpackPluginDemo的目录结构如下：
├── app
├── package-lock.json
├── package.json
├── src
│ └── index.js
└── webpack.config.js
```
### 1. 安装webpack
在webpackPluginDemo根目录下安装webpack:
```
npm install --save-dev webpack
```
### 2.入口文件index.js
```
document.write('webpack系列之plugin的基本使用！');
```
### 3.webpack配置文件webpack.config.js
```
const webpack = require('webpack') 
module.exports = {
    entry:  __dirname + "/src/index.js",  //入口文件
    output: {
        path: __dirname + "/app",  //打包后的文件存放的地方
        filename: "bundle.js" //打包后输出文件的文件名
    },
    plugins: [
        new webpack.BannerPlugin('版权所有，翻版必究')
    ],
}
```
>注意：BannerPlugin为内置插件，如果是其它的外置插件，则需在使用前要先安装。

### 4.执行打包命令
```
➜  webpackPluginDemo webpack
Hash: 16453f43abe665633286
Version: webpack 2.4.1
Time: 70ms
    Asset     Size  Chunks             Chunk Names
bundle.js  2.86 kB       0  [emitted]  main
   [0] ./src/index.js 210 bytes {0} [built]
```

### 5.查看结果
打包成功，可以看到app目录下面已经生成了bundle.js，打开bundle.js会发现版权信息已经加上了

## 五.常用插件
常用插件
- BannerPlugin：对所有的文件打包后添加一个版权声明
- uglifyjs-webpack-plugin： 对JS进行压缩混淆
- HtmlWebpackPlugin：可以根据模板自动生成html代码，并自动引用css和js文件
- Hot Module Replacement：在每次修改代码保存后，浏览器会自动刷新，实时预览修改后的效果
- copy-webpack-plugin：通过Webpack来拷贝文件
- extract-text-webpack-plugin：将js文件和css文件分别单独打包，不混在一个文件中
- DefinePlugin 编译时配置全局变量，这对开发模式和发布模式的构建允许不同的变量时非常有用
- optimize-css-assets-webpack-plugin 不同组件中重复的css可以快速去重