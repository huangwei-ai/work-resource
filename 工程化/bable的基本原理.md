## 什么是 Babel？
>官方的解释 Babel 是一个 JavaScript 编译器，用于将 ECMAScript 2015+ 版本的代码转换为向后兼容的 JavaScript 语法，以便能够运行在当前版本和旧版本的浏览器或其他环境中。简单来说 

Babel 的工作就是：
- 语法转换
- 通过 Polyfill 的方式在目标环境中添加缺失的特性
- JS 源码转换

## Babel 的基本原理
原理很简单，核心就是`AST (抽象语法树)`。首先将源码转成抽象语法树，然后对语法树进行处理生成新的语法树，最后将新语法树生成新的 JS 代码，整个编译过程可以分为 3 个阶段 `parsing (解析)、transforming (转换)、generating (生成)`，都是在围绕着 AST 去做文章，话不多说上图：
![image text](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/3/1/170949352d2ba854~tplv-t2oaga2asx-watermark.awebp)
整个过程很清晰，但是，好多东西都是看着简单，但是实现起来贼复杂，比如这里说到的 AST，要是你觉得你对 AST 已经信手拈来了，，Babel 只负责编译新标准引入的新语法，比如 Arrow function、Class、ES Module 等，它不会编译原生对象新引入的方法和 API，比如 Array.includes，Map，Set 等，这些需要通过 Polyfill 来解决，文章后面会提到。
## Babel 的使用
### 运行 babel 所需的基本环境
#### babel/cli
```
npm install i -S @babel/cli
```
@babel/cli 是 Babel 提供的内建命令行工具。提到 @babel/cli 这里就不得不提一下 @babel/node ，这哥俩虽然都是命令行工具，但是使用场景不同，`babel/cli 是安装在项目中`，而 `@babel/node 是全局安装`。

#### @babel/core
```
npm install i -S @babel/core
```
安装完 @babel/cli 后就在项目目录下执行babel test.js会报找不到 @babel/core 的错误，**因为 @babel/cli 在执行的时候会依赖 @babel/core 提供的生成 AST 相关的方法，所以安装完 @babel/cli 后还需要安装 @babel/core**。
安装完这两个插件后，如果在 Mac 环境下执行会出现 command not found: babel，这是因为 @babel/cli是安装在项目下，而不是全局安装，所以无法直接使用 Babel 命令,需要在 package.json 文件中加上下面这个配置项:
```
"scripts": {
   "babel":"babel"
 }
```
然后执行 `npm run babel ./test.js`，顺利生成代码，此时生成的代码并没有被编译，因为 Babel 将原来集成一体的各种编译功能分离出去，独立成插件，要编译文件需要安装对应的插件或者预设，我们经常看见的什么 `@babel/preset-stage-0、@babel/preset-stage-1，@babel/preset-env` 等就是干这些活的。那这些插件和预设怎么用呢？下面就要说到 Babel 的配置文件了，这些插件需要在配置文件中交代清楚，不然 Babel 也不知道你要用哪些插件和预设。


#### 安装完基本的包后，就是配置 Babel 配置文件，Babel 的配置文件有四种形式：

- babel.config.js

在项目的根目录（package.json 文件所在目录）下创建一个名为 babel.config.js 的文件，并输入如下内容。
```
module.exports = function (api) {
 api.cache(true);
 const presets = [ ... ];
 const plugins = [ ... ];
 return {
   presets,
   plugins
 };
}
```
具体 babel.config.js 配置

- .babelrc

在你的项目中创建名为 .babelrc 的文件
```
{
 "presets": [...],
 "plugins": [...]
}
```
.babelrc文档

- .babelrc.js

与 .babelrc 的配置相同，你可以使用 JavaScript 语法编写。
```
const presets = [ ... ];
const plugins = [ ... ];
module.exports = { presets, plugins };
```

- package.json
还可以选择将 .babelrc 中的配置信息写到 package.json 文件中
```
{
 ...
 "babel": {
   "presets": [ ... ],
   "plugins": [ ... ],
 }
}
```

四种配置方式作用都一样，你就合着自己的口味来，那种看着顺眼，你就翻它。

## 插件(Plugins)
插件是用来定义如何转换你的代码的。在 Babel 的配置项中填写需要使用的插件名称，Babel 在编译的时候就会去加载 node_modules 中对应的 npm 包，然后编译插件对应的语法。
.babelrc
```
{
  "plugins": ["transform-decorators-legacy", "transform-class-properties"]
}
```
#### 插件执行顺序
插件在预设(Presets) 前运行。

插件的执行顺序是从左往右执行。也就是说在上面的示例中，Babel 在进行 AST 遍历的时候会先调用 `transform-decorators-legacy` 插件中定义的转换方法，然后再调用 `transform-class-properties` 中的方法。

#### 插件传参
参数是由插件名称和参数对象组成的一个数组。
```
{
    "plugins": [
        [
            "@babel/plugin-proposal-class-properties", 
            { "loose": true }
        ]
    ]
}
```
#### 插件名称
插件名称如果为` @babel/plugin-XX`，可以使用短名称`@babel/XX` ，如果为 `babel-plugin-xx`，可以直接使用 `xx`。

#### 自定义插件
大部分时间我们都是在用别人的写的插件，但是有时候我们总是想秀一下，自己写一个 Babel 插件，那应该怎么操作呢？

- 1、插件加载

要致富先修路，要用自己写的插件首先得知道怎么使用自定义的插件。一种方式是将自己写的插件发布到 npm 仓库中去，然后本地安装，然后在 Babel 配置文件中配置插件名称就好了:
```
npm install @babel/plugin-myPlugin
```
.babelrc
```
{
 "plugins": ["@babel/plugin-myPlugin"]
}
```
另外一种方式就是不发布，直接将写好的插件放在项目中，然后在 babel 配置文件中通过访问相对路径的方式来加载插件：
.babelrc
```
{
 "plugins": ["./plugins/plugin-myPlugin"]
}
```
>第一种通过 npm 包的方式一般是插件功能已经完善和稳定后使用，第二种方式一般在开发阶段，本地调试时使用。

- 2、编写插件

插件实际上就是在`处理 AST 抽象语法树`，所以编写插件只需要做到下面三点：
- 确认我们要修改的节点类型
- 找到 AST 中需要修改的属性
- 将 AST 中需要修改的属性用新生成的属性对象替换


好像少了生成 AST 对象和生成源码的步骤，不急，后面会讲。说一千道一万不如一个例子来的实在，下面实现一个预计算(在编译阶段将表达式计算出来)的插件：
```
const result = 1 + 2;
```
转换成：
```
const result = 3;
```
在写插件前你需要明确转换前后的 AST 长什么样子，就好像整容一样，你总得选个参考吧。 AST explorer 你值得拥有。
转换前：
![image text](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/3/1/170949352d8e1ebc~tplv-t2oaga2asx-watermark.awebp)
转换后：
![image text](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/3/1/170949352f5e06fc~tplv-t2oaga2asx-watermark.awebp)
找到差别，然后就到了用代码来解决问题的时候了
```
let babel = require('@babel/core');
let t = require('babel-types');
let preCalculator={
   visitor: {
       BinaryExpression(path) {
           let node = path.node;
           let left = node.left;
           let operator = node.operator;
           let right = node.right;
           if (!isNaN(left.value) && !isNaN(right.value)) {
               let result = eval(left.value + operator + right.value);
              //生成新节点，然后替换原先的节点
               path.replaceWith(t.numericLiteral(result));
                //递归处理 如果当前节点的父节点配型还是表达式 
                if (path.parent && path.parent.type == 'BinaryExpression') {
                   preCalculator.visitor.BinaryExpression.call(null,path.parentPath);
               }
           }
       }
   }
}

const result = babel.transform('const sum = 1+2+3',{
   plugins:[
       preCalculator
   ]
});
```
上面这段代码，Babel 在编译的时候会深度遍历 AST 对象的每一个节点，采用访问者的模式，每个节点都会去访问插件定义的方法，如果类型和方法中定义的类型匹配上了，就进入该方法修改节点中对应属性。在节点遍历完成后，新的 AST 对象也就生成了。babel-types 提供 AST 树节点类型对象。

上面这样写只是为了我们开发测试方便，其实最终的完整体是下面这样的：
```
const types = require('babel-types');
const visitor = {
   BinaryExpression(path) {//需要处理的节点路径
           let node=path.node;
           let left=node.left;
           let operator=node.operator;
           let right=node.right;
           if (!isNaN(left.value) && !isNaN(right.value)) {
               let result=eval(left.value+operator+right.value);
               path.replaceWith(t.numericLiteral(result));
               if (path.parent&& path.parent.type == 'BinaryExpression') {
                   preCalculator.visitor.BinaryExpression.call(null,path.parentPath);
               }
           }
   }
}
module.exports = function(babel){
   return {
       visitor
   }
}
```
我们在插件中只需要修改匹配上的 AST 属性，不需要关注源码到 AST 以及新 AST 到源码的过程，这些都是 Babel 去干的事，我们干好自己的活就好了，其他的交给 babel。这也就解释了我上面的步骤中为嘛没有 AST 的生成和源码的生成，那就不是我们在插件中干的事儿。

## 预设（Presets）

>预设就是一堆插件(Plugin)的组合，从而达到某种转译的能力，就比如 react 中使用到的 `@babel/preset-react` ，它就是下面几种插件的组合。
```
@babel/plugin-syntax-jsx
@babel/plugin-transform-react-jsx
@babel/plugin-transform-react-display-name
```
当然我们也可以手动的在 plugins 中配置一系列的 plugin 来达到目的，就像这样：
```
{
  "plugins":["@babel/plugin-syntax-jsx","@babel/plugin-transform-react-jsx","@babel/plugin-transform-react-display-name"]
}
```
但是这样一方面显得不那么优雅，另一方面增加了使用者的使用难度。如果直接使用预设就清新脱俗多了~
``` 
{
  "presets":["@babel/preset-react"]
}
```

### 预设(Presets)的执行顺序
前面提到插件的执行顺序是从左往右，而预设的执行顺序恰好反其道行之，它是从右往左
```
{
  "presets": [
    "a",
    "b",
    "c"
  ]
}
```
它的执行顺序是 c、b、a，是不是有点奇怪，这主要是为了确保向后兼容，因为大多数用户将 "es2015" 放在 "stage-0" 之前。
### 自定义预设(Presets)

这种场景一般很少，在这个拿来主义的时代，插件我们都很少写，就更别说自定义预设了。不过前面插件我们都说了怎么写了，预设咱也不能冷落她呀。

前面提到预设就是已有插件的组合，主要就是为了避免使用者配置过多的插件，通过预设把插件收敛起来，其实写起来特别简单，前提是你已经确定好要用哪些插件了。
```
import { declare } from "@babel/helper-plugin-utils";
import pluginA from "myPluginA";
import pluginB from "myPluginB"
export default declare((api, opts) => {
  const pragma = opts.pragma;
  return {
    plugins: [
      [
        pluginA,
        {pragma}//插件传参
      ],
      pluginB
    ]
  };
});
```
其实就是把 Babel 配置中的 plugins 配置放到 presets 中了，实质上还是在配置 Plugins，只是写 Presets 的人帮我们配置好了。

### 其他预设
####  @babel/preset-stage-xxx

`@babel/preset-stage-xxx` 是 ES 在不同阶段语法提案的转码规则而产生的预设，随着被批准为 ES 新版本的组成部分而进行相应的改变（例如 ES6/ES2015）。
提案分为以下几个阶段：
- stage-0 - 设想（Strawman）：只是一个想法，可能有 Babel 插件，stage-0 的功能范围最广大，包含 stage-1 , stage-2 以及 stage-3 的所有功能
- stage-1 - 建议（Proposal）：这是值得跟进的
- stage-2 - 草案（Draft）：初始规范
- stage-3 - 候选（Candidate）：完成规范并在浏览器上初步实现
- stage-4 - 完成（Finished）：将添加到下一个年度版本发布中

#### @babel/preset-es2015
preset-es2015 是仅包含 ES6 功能的 Babel 预设。
实际上在 Babel7 出来后上面提到的这些预设 stage-x，preset-es2015 都可以废弃了，因为 @babel/preset-env 出来一统江湖了。

#### @babel/preset-env
前面两个预设是从 ES 标准的维度来确定转码规则的，而 @babel/preset-env 是根据浏览器的不同版本中缺失的功能确定代码转换规则的，在配置的时候我们只需要配置需要支持的浏览器版本就好了，**@babel/preset-env 会根据目标浏览器生成对应的插件列表然后进行编译**：
```
{
 "presets": [
   ["env", {
     "targets": {
       "browsers": ["last 10 versions", "ie >= 9"]
     }
   }],
 ],
 ...
}
```
在默认情况下 @babel/preset-env 支持将 JS 目前最新的语法转成 ES5，但需要注意的是，如果你代码中用到了还没有成为 JS 标准的语法，该语法暂时还处于 stage 阶段，这个时候还是需要安装对应的 stage 预设，不然编译会报错。
```
{
 "presets": [
   ["env", {
     "targets": {
       "browsers": ["last 10 versions", "ie >= 9"]
     }
   }],
 ],
 "stage-0"
}   
```
虽然可以采用默认配置，但如果不需要照顾所有的浏览器，还是建议你配置目标浏览器和环境，这样可以保证编译后的代码体积足够小，因为在有的版本浏览器中，新语法本身就能执行，不需要编译。@babel/preset-env 在默认情况下和 preset-stage-x 一样只编译语法，不会对新方法和新的原生对象进行转译，例如：
```
const arrFun = ()=>{}
const arr = [1,2,3]
console.log(arr.includes(1))
```
转换后
```
"use strict";

var arrFun = function arrFun() {};

var arr = [1, 2, 3];
console.log(arr.includes(1));
```
箭头函数被转换了，但是 Array.includes 方法，并没有被处理，这个时候要是程序跑在低版本的浏览器上，就会出现 `includes is not function` 的错误。这个时候就需要 polyfill 闪亮登场了。

## Polyfill
`polyfill` 的翻译过来就是垫片，垫片就是垫平不同浏览器环境的差异，让大家都一样。
### @babel/polyfill
@babel/polyfill 模块可以模拟完整的 ES5 环境。
安装:
```
npm install --save @babel/polyfill
```
注意 @babel/polyfill 不是在 Babel 配置文件中配置，而是在我们的代码中引入。
```
import '@babel/polyfill';
const arrFun = ()=>{}
const arr = [1,2,3]
console.log(arr.includes(1))
Promise.resolve(true)
```
编译后:
```
require("@babel/polyfill");
var arrFun = function arrFun() {};
var arr = [1, 2, 3];
console.log(arr.includes(1));
Promise.resolve(true);
```
这样在低版本的浏览器中也能正常运行了。
不知道大家有没有发现一个问题，这里是`require("@babel/polyfill")`将整个 @babel/polyfill 加载进来了，但是在这里我们需要处理 Array.includes 和 Promise 就好了，如果这样就会导致我们最终打出来的包体积变大，显然不是一个最优解。要是能按需加载就好了。其实 Babel 早就为我们想好了。

### useBuiltIns
回过头来再说 @babel/preset-env，他出现的目的就是实现民族大统一，连 stage-x 都干掉了，又怎么会漏掉 Polyfill 这一功能，在 @babel/preset-env 的配置项中提供了 useBuiltIns 这一参数，只要在使用 @babel/preset-env 的时候带上他，Babel 在编译的时候就会自动进行 Polyfill ，不再需要手动的在代码中引入@babel/polyfill 了，同时还能做到按需加载
```
{
  "presets": [
    "@babel/preset-flow",
    [
      "@babel/preset-env",
      {
        "targets": {
          "node": "8.10"
        },
        "corejs": "3", // 声明 corejs 版本
        "useBuiltIns": "usage"
      }
    ]
  ]
}
```
注意，这里需要配置一下 corejs 的版本号，不配置编译的时候会报警告。讲都讲到这里了就再顺便提一嘴 `useBuiltIns 的机构参数`：

- false：此时不对Polyfill 做操作，如果引入 @babel/polyfill 则不会按需加载，会将所有代码引入
- usage：会根据配置的浏览器兼容性，以及你代码中使用到的 API 来进行 Polyfill ，实现按需加载
- entry：会根据配置的浏览器兼容性，以及你代码中使用到的 API 来进行 Polyfill ，实现按需加载，不过需要在入口文件中手动加上`import ' @babel/polyfill'`

编译后：
```
"use strict";
require("core-js/modules/es.array.includes");
require("core-js/modules/es.object.to-string");
require("core-js/modules/es.promise");
var arrFun = function arrFun() {};
var arr = [1, 2, 3];
console.log(arr.includes(1));
Promise.resolve(true);
```
这个时候我们再借助 Webpack 编译后，产出的代码体积会大大减小。

其实 Babel 在编译中会使用一些辅助函数，比如：
```
class Person {
  constructor(){}
  say(word){
    console.log(":::",word)
  }
}
```
编译后：
```
"use strict";
require("core-js/modules/es.object.define-property");
function _classCallCheck(instance, Constructor) { if (!(instance instanceof Constructor)) { throw new TypeError("Cannot call a class as a function"); } }
function _defineProperties(target, props) { for (var i = 0; i < props.length; i++) { var descriptor = props[i]; descriptor.enumerable = descriptor.enumerable || false; descriptor.configurable = true; if ("value" in descriptor) descriptor.writable = true; Object.defineProperty(target, descriptor.key, descriptor); } }
function _createClass(Constructor, protoProps, staticProps) { if (protoProps) _defineProperties(Constructor.prototype, protoProps); if (staticProps) _defineProperties(Constructor, staticProps); return Constructor; }
var Person =
/*#__PURE__*/
function () {
  function Person() {
    _classCallCheck(this, Person);
  }
  _createClass(Person, [{
    key: "say",
    value: function say(word) {
      console.log(":::", word);
    }
  }]);
  return Person;
}();
```
这些方法会被 inject 到每个文件中，没法做到复用，这样也会导致打包体积的增加。
没事儿，逢山开路遇水搭桥，是时候让`@babel/plugin-transform-runtime` 登场了。

### @babel/plugin-transform-runtime
@babel/plugin-transform-runtime 可以`让 Babel 在编译中复用辅助函数，从而减小打包文件体积`，不信你看：
```
npm install --save-dev @babel/plugin-transform-runtime
npm install --save @babel/runtime
```
顺便说一下，这一对 CP 要同时出现，形影不离，所以安装的时候你就一起装上吧~

配置 Babel：
```
{
    "presets": [
        [
            "@babel/preset-env",
            {
                "useBuiltIns": "usage",
                "corejs": 3
            }
        ]
    ],
    "plugins": [
       "@babel/plugin-transform-runtime"
    ]
}
```
结果：
```
"use strict";
var _interopRequireDefault = require("@babel/runtime/helpers/interopRequireDefault");
var _classCallCheck2 = _interopRequireDefault(require("@babel/runtime/helpers/classCallCheck"));
var _createClass2 = _interopRequireDefault(require("@babel/runtime/helpers/createClass"));
var Person =
/*#__PURE__*/
function () {
  function Person() {
    (0, _classCallCheck2["default"])(this, Person);
  }
  (0, _createClass2["default"])(Person, [{
    key: "say",
    value: function say(word) {
      console.log(":::", word);
    }
  }]);
  return Person;
}();
```
这些用到的辅助函数都从 `@babel/runtime `中去加载，这样就可以做到代码复用了。

---
参考资料
作者：政采云前端团队
链接：https://juejin.cn/post/6844904079118827533