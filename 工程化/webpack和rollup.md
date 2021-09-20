>2017年4月份的时候，Facebook将React的构建工具换成了Rollup。很多人就有疑问了，Webpack不也是Facebook团队开发的吗，为什么不使用它而是要换成第三方构建工具呢？先别急，等你看完这篇文章，你就知道为什么了。

## 一、Webpack

诞生于2012年，目前Javascript社区使用得比较多的构建工具。它的出现，**解决了当时的构建工具不能处理的问题——构建复杂的单页面应用(SPA)**。它是一个强力的**模块打包器**。 所谓包(bundle)就是一个 JavaScript 文件，它把**一堆资源(assets)合并在一起，以便它们可以在同一个文件请求中发回给客户端**。 包中可以包含 JavaScript、CSS 样式、HTML 以及很多其它类型的文件。

### Webpack的特点

#### 代码分割

Webpack 有两种组织模块依赖的方式，同步和异步。异步依赖作为分割点，形成一个新的块。在优化了依赖树后，每一个异步区块都作为一个文件被打包。

#### Loader(加载器)

Webpack 本身只能处理原生的 JavaScript 模块，但是 loader 转换器可以将各种类型的资源转换成 JavaScript 模块。这样，任何资源都可以成为 Webpack 可以处理的模块。

#### 智能解析

Webpack 有一个智能解析器，几乎可以处理任何第三方库，无论它们的模块形式是 CommonJS、 AMD 还是普通的 JS 文件。甚至在加载依赖的时候，允许使用动态表达式 require("./templates/" + name + ".jade")。

#### 插件系统

Webpack 还有一个功能丰富的插件系统。大多数内容功能都是基于这个插件系统运行的，还可以开发和使用开源的 Webpack 插件，来满足各式各样的需求。

### 开始使用

- 安装

目前webpack最新版本是3.0.0

```npm i webpack -g npm i webpack@version -g```

- 配置

在项目添加webpack.config.js
```
const path = require('path');const webpack = require('webpack');module.exports = { entry: './src/main.js', output: { path: path.resolve(__dirname, './dist'), publicPath: '/dist/', filename: 'build.js' }, module: { rules: [ { test: /\.vue$/, loader: 'vue-loader', options: { loaders: {} // other vue-loader options go here } }, { test: /\.js$/, loader: 'babel-loader', exclude: /node_modules/ }, { test: /\.(png|jpg|gif|svg)$/, loader: 'file-loader', options: { name: '[name].[ext]?[hash]' } } ] }, resolve: { alias: { 'vue$': 'vue/dist/vue.common.js' } }, devServer: { historyApiFallback: true, noInfo: true }, performance: { hints: false }, devtool: '#eval-source-map'}if (process.env.NODE_ENV === 'production') { module.exports.devtool = '#source-map'; module.exports.plugins = (module.exports.plugins || []).concat([ new webpack.DefinePlugin({ 'process.env': { NODE_ENV: '"production"' } }), new webpack.optimize.UglifyJsPlugin({ sourceMap: true, compress: { warnings: false } }), new webpack.LoaderOptionsPlugin({ minimize: true }) ]);}
```
打开命令控制台，执行：

```webpack# or webpack --config webpack.config.js```

此时你应该可以在项目目录的dist文件夹里找到打包好的文件了。

其他使用方式可参照官方文档:英文：https://webpack.js.org/configuration/中文：https://doc.webpack-china.org/configuration/

## 二、Rollup

>Rollup是下一代JavaScript模块打包工具。开发者可以在你的应用或库中使用ES2015模块，然后高效地将它们打包成一个单一文件用于浏览器和Node.js使用。 Rollup最令人激动的地方，就是能让打包文件体积很小。这么说很难理解，更详细的解释：相比其他JavaScript打包工具，Rollup总能打出更小，更快的包。因为Rollup基于ES2015模块，比Webpack和Browserify使用的CommonJS模块机制更高效。这也让Rollup从模块中删除无用的代码，即**tree-shaking**变得更容易。


### Rollup的特点

#### Tree-shaking

这个特点，是Rollup最初推出时的一大特点。Rollup通过对代码的静态分析，分析出冗余代码，在最终的打包文件中将这些冗余代码删除掉，进一步缩小代码体积。这是目前大部分构建工具所不具备的特点(Webpack 2.0+已经支持了，但是我本人觉得没有Rollup做得干净)。

### ES2015模块打包支持

这个也是其他构建工具所不具备的。Rollup直接不需要通过babel将import转化成Commonjs的require方式，极大地利用ES2015模块的优势。

### 使用

先在全局安装好rollup

```npm i rollup -g```

然后我们再创建一个简单的项目：

```mkdir -p my-rollup-project/srccd my-rollup-project```

编写好入口文件：
```
// src/main.jsimport foo from './foo.js';export default function () { console.log(foo);}
```
然后，创建一个名为

foo.js

的文件
```
// src/foo.jsexport default 'hello world!';
```
最后，打开命令行，执行下面的命令吧：
```
rollup src/main.js --format cjs
```
打包结果：
```
'use strict';var foo = 'hello world!';var main = function () { console.log(foo);};module.exports = main;
```
你也可以打包出一个名为

bundle.js

的文件：
```
rollup src/main.js --format cjs --output bundle.js# or `rollup main.js -f cjs -o bundle.js`
```
其他使用方式可查看官网文档：https://rollupjs.org

## 三、Webpack与Rollup的对比

其实，通过分别对Webpack和Rollup的介绍，不难看出，Webpack和Rollup在不同场景下，都能发挥自身优势作用。Webpack对于**代码分割和静态资源导入**有着“先天优势”，并且支持**热模块替换(HMR)**，而Rollup并不支持，所以当项目需要用到以上，则可以考虑选择Webpack。但是，Rollup对于代码的Tree-shaking和ES6模块有着**算法优势上的支持**，若你项目只需要打包出一个简单的bundle包，并是**基于ES6模块开发的，可以考虑使用Rollup**。其实Webpack从2.0开始支持Tree-shaking，并在使用babel-loader的情况下支持了es6 module的打包了，实际上，Rollup已经在渐渐地失去了当初的优势了。但是它并没有被抛弃，反而因其简单的API、使用方式被许多库开发者青睐，如React、Vue等，都是使用Rollup作为构建工具的。而Webpack目前在中大型项目中使用得非常广泛。最后，用一句话概括就是：

**在开发应用时使用 Webpack，开发库时使用 Rollup**


## 四、总结

Webpack和Rollup作为构建工具，都有着各自的优势和各自的使用场景，我们不能因为他们的一些缺点而弃之，相反，我们在开发过程中，若是能利用好它们的优点，并能对我们的生产效率有着极大的提高作用。

更多网易技术、产品、运营经验分享请访问网易云社区。