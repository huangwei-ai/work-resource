>模块热更新(热替换)，其目的是为了加快用户的开发速度，提高编程体验。它并不适用于生产环境，这意味着它应当只在开发环境使用

  本文假设用户已经对webpack和前端工程化有一定的了解，否则建议先看看 前端工程化与「Webpack」一文或官方网站

## 什么是模块热替换
> 模块热替换（HMR - Hot Module Replacement）是 webpack 提供的最有用的功能之一。它允许在运行时替换，添加，删除各种模块，而无需进行完全刷新重新加载整个页面，其思路主要有以下几个方面

- 保留在完全重新加载页面时丢失的应用程序的状态
- 只更新改变的内容，以节省开发时间
- 调整样式更加快速，几乎等同于就在浏览器调试器中更改样式
 
## 启用HMR
  HMR的启用十分简单，这归功于webpack内置插件使用上的便利。我们要做的仅仅是更新webpack-dev-server的配置，和使用webpack内置的HMR插件

  一个带有热替换功能的webpack.config.js 文件的配置如下，做了这么几件事

- 引入了webpack库
- 使用了new webpack.HotModuleReplacementPlugin()
- 设置devServer选项中的hot字段为true
```
  const path = require('path');
  const HtmlWebpackPlugin = require('html-webpack-plugin');
  const CleanWebpackPlugin = require('clean-webpack-plugin');
  const webpack = require('webpack');

  module.exports = {
    entry: {
      app: './src/index.js'
    },
    devtool: 'inline-source-map',
    devServer: {
      contentBase: './dist',
      hot: true
    },
    plugins: [
      new CleanWebpackPlugin(['dist']),
      new HtmlWebpackPlugin({
        title: 'Hot Module Replacement'
      }),
      new webpack.HotModuleReplacementPlugin()
    ],
    output: {
      filename: '[name].bundle.js',
      path: path.resolve(__dirname, 'dist')
    }
  };
```
  模块的热替换相对来说比较难掌握，很容以一不小心就犯错导致失效，好在存在很多 loader，使得模块热替换的过程变得更容易。
 

## 案例： HMR 修改样式表
  借助于 style-loader 的帮助，CSS 的模块热替换实际上是相当简单的。当更新 CSS 依赖模块时，此 loader 在后台使用 module.hot.accept 来修补(patch) <style> 标签。

  需要使用npm安装如下两个加载器
```
npm install --save-dev style-loader css-loader
```
接下来我们来更新 webpack.config.js 中module的配置，让这两个 loader 生效。
```
module: {
  rules: [
    {
      test: /\.css$/,
      use: ['style-loader', 'css-loader']
    }
  ]
},
```
至此，在修改css样式时，经过webpack编译过后的文件就已经被这两个加载器转化过了，HMR将自动更模块内容

参考资料
作者：果汁凉茶丶
链接：https://www.jianshu.com/p/45c150c4aece