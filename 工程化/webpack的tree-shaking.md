## webpack中tree-shaking技术介绍
之前介绍过webpack3的新特性，里面提到webpack2支持了ES6的import和export，不需要将ES6的模块先转成CommonJS模块，然后再进行打包处理。正基于此，webpack2引入了tree-shaking技术，**能够在模块的层面上做到打包后的代码只包含被引用并被执行的模块，而不被引用或不被执行的模块被删除掉，以起到减包的效果**。

JS 文件绝大多数通过网络进行加载，然后执行。**DCE（dead code elimination）可以使得加载文件的大小更小，整体执行时间更短**。**tree shaking 就是通常用于描述移除 JavaScript 上下文中的未引用代码(dead-code)**。它依赖于 ES2015 模块语法的 静态结构 特性，例如 import 和 export。

## webpack的tree-shaking案例
下面结合实际代码来解释webpack2是如何实现tree-shaking的。

src中index.js为入口文件，module.js是测试的模块文件，dist中是产出的文件。
![image text](https://images2018.cnblogs.com/blog/605230/201711/605230-20171127195301847-2060238992.png)


根据webpack官网的提示，webpack支持tree-shaking，**需要修改配置文件，指定babel处理js文件时不要将ES6模块转成CommonJS模块**，具体做法就是：

**在.babelrc设置babel-preset-es2015的modules为fasle**，表示不对ES6模块进行处理。
```
// .babelrc文件
{
  "presets": [
    <strong>["es2015", { "modules": false }]</strong>
  ],
  "comments": false
}
```
在webpack.config.js中设置babel-preset-es2015的modules为fasle，表示不对ES6模块进行处理。　　
```
+ View Code
```
然后在module.js文件中创建三个模块sayHello，sayBye，sayHi，并在index.js引用sayHello，sayHi；
```
// module.js
export const sayHello = name => `Hello ${name}!`;
export const sayBye = name => `Bye ${name}!`;
export const sayHi = name => `Hi ${name}!`;
```
```
// index.js
import { sayHello } from './module';
import { sayHi } from './module';
const element = document.createElement('h1');
element.innerHTML = sayHello('World') + sayHi('my friend');
document.body.appendChild(element);
```
然后在当前目录执行 webpack 命令后，产出bundle.js的代码如下
```
/* 0 */
/***/ (function(module, __webpack_exports__, __webpack_require__) {
 
"use strict";
<strong>/* harmony export (binding) */ __webpack_require__.d(__webpack_exports__, "a", function() { return sayHello; });
/* unused harmony export sayBye */
/* harmony export (binding) */ __webpack_require__.d(__webpack_exports__, "b", function() { return sayHi; });</strong>
 
 
var sayHello = function sayHello(name) {
  return "Hello " + name + "!";
};
var sayBye = function sayBye(name) {
  return "Bye " + name + "!";
};
 
var sayHi = function sayHi(name) {
  return "Hi " + name + "!";
};
 
/***/ }),
/* 1 */
/***/ (function(module, __webpack_exports__, __webpack_require__) {
 
"use strict";
<strong>Object.defineProperty(__webpack_exports__, "__esModule", { value: true });
/* harmony import */ var __WEBPACK_IMPORTED_MODULE_0__module__ = __webpack_require__(0);</strong>
 
 
 
var element = document.createElement('h1');
element.innerHTML = Object(<strong>__WEBPACK_IMPORTED_MODULE_0__module__["a" /* sayHello */]</strong>)('World') + Object(<strong>__WEBPACK_IMPORTED_MODULE_0__module__["b" /* sayHi */]</strong>)(' to meet you');
document.body.appendChild(element);
 
/***/ })
```

从上面可以知道，sayBye模块被打上了`unused harmony export`标签，sayHello和sayHi被设置为`__webpack_exports__`的属性，在入口文件中通过读取`__webpack_exports__`的属性取出。

bundle.js文件虽然对多余的模块进行了标记，但是并没有删除，这是因为webpack还没有执行压缩混淆操作，可以通过webpack -p命令对产出进行压缩处理，这时候会把打了unused harmony export 标签的模块删除掉。

## webpack的tree-shaking的局限性
- （1）只能是静态声明和引用的ES6模块，不能是动态引入和声明的；

在打包阶段对冗余代码进行删除，就需要webpack需要在打包阶段确定模块文件的内部结构，而ES模块的引用和输出必须出现在文件结构的第一级`（'import' and 'export' may only appear at the top level）`，否则会报错。
```
// webpack编译时会报错
if (condition) {
  import module1 from './module1';
} else {
  import module2 from './module2';
}
```

而CommonJS模块支持动态结构的，所以不能对CommonJS模块进行tree-shaking处理。

- （2）只能处理模块级别，不能处理函数级别的冗余；

 因为webpack的tree-shaking是基于模块间的依赖关系，所以并不能对模块内部自身的无用代码进行删除。

- （3）只能处理JS相关冗余代码，不能处理CSS冗余代码。

目前webpack只对JS文件的依赖进行了处理，CSS的冗余并没有给出很好的工具。最近听了一个讲座，提到了webpack-css-treeshaking-plugin，该插件基于AST对CSS冗余代码进行了很好的处理。

 