对于 Webpack 的应用，其实最主要的就是在 Loader 和 Plugin 上面。

那么想用好，首先就要了解清楚这两个。

## 一、打包流程
 在对比前，先来了解下 Webpack 的打包流程。

- 初始化参数：从 shell 语句和配置文件中读取，并合并参数，得出最终的参数
- 开始编译：用上一步得到的参数初始化 Compiler 对象，加载所有插件，执行对象的 run 方法开始编译
- 确定入口：根据配置文件中的 entry 找到所有入口文件
- 编译模块：从入口文件出发，调用所有配置的 Loader 对文件进行预编译，找出入口文件所依赖的文件，依次递归找出所有依赖文件并一 一进行编译（编译文件，收集依赖关系）
- 完成模块编译：经过上一步，得到编译后的文件以及依赖关系
- 输出资源：根据入口和模块的依赖关系，组装成一个个包含多个模块的 Chunk，再把这些 Chunk 转换成文件添加到输出目录，这里是可以修改输出内容的最后机会
- 输出完成：在确定好输出内容，根据配置确定输出的路径、文件名，把要输出内容写入到文件系统

## 二、两者区别
### 功能角度区分
- loader
```
1、loader 从字面意思 加载 的意思

2、由于 webpack 本身只能打包 commonJS 规范的 js 文件，对于 css、图片等文件没办法打包，就需要第三方模块进行打包（转换器）

3、loader 虽然扩展了 webpack，但是只专注于转化文件（transform）这一领域

4、loader 是运行在 NodeJS 中

5、仅仅为了打包
```
- plugins
```
1、plugins 完成是 loader 完成不了的功能

2、plugins 也是扩展 webpack，但是是作用于 webpack 本身的；并且不仅限在打包上面，有根据丰富的功能；从打包优化到压缩，重新定义环境变量功能强大到可以用来处理各种各样的任务

3、插件可以携带参数，所以一般用 new
```
- 运行时机区分
```
1、loader 运行在打包文件前（loader 是在模块加载前的预处理文件，对应上面打包流程中的 4）

2、plugins 在编译的整个周期都起作用，是基于事件机制，监听 webpack 打包过程中的某些节点执行任务
```

## 三、常见的 Loader
- 处理 CSS 的 loade：style-loader、css-loader、less-loader、scss-loader
- 处理图片的 loader：url-loader、file-loader
- 处理 html 的 loader：html-loader
- 处理 vue 文件的 loader：vue-loader、vue-style-loader

## 四、常见的 Plugin
- html-webpack-plugin：创建空的 HTML 或者根据模板创建，并自动引入打包后的资源（js/css），同时也可以配置压缩 js 和 html
- copy-webpack-plugin：用于复制不用打包的文件到打包后的指定位置
- mini-css-extract-plugin：把 css 单独提前，并用 link 引入（这样不容易出现闪屏，js 打包也会变小）
- optimize-css-assets-webpack-plugin：压缩 CSS 文件
- DllReferencePlugin：告诉 webpack 哪些不用打包，并且使用的名字也要变
- add-asset-html-webpack-plugin：将某个文件打包输出，并自动引入（配合上面的 dll 使用）
- webpack-bundle-analyzer：打包构建分析，会有直接的页面可以展示（可配置关闭）
- compression-webpack-plugin：压缩打包后的文件