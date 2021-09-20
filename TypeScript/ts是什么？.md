
## 什么是 TypeScript
>TypeScript 是微软开发一款开源的编程语言，它是 JavaScript 的一个超集，本质上是为 JavaScript 增加了静态类型声明。任何的 JavaScript 代码都可以在其中使用，不会有任何问题。TypeScript 最终也会被编译成 JavaScript，使其在浏览器、Node 中等环境中使用。

TypeScript 是一种由微软开发的自由和开源的编程语言，它是 JavaScript 的一个超集，扩展了 JavaScript 的语法。

TypeScript 是面向对象的 JavaScript。(类)

简单对理解，TypeScript 就是带有类型检测的 JavaScript。

## 安装 TypeScript

在控制台运行一下命令：
```
npm install typesrcipt -g
```
这条命令会在全局安装 typescript，并且安装 tsc 命令，运行以下命令可以查看当前版本（确认安装成功）：
```
tsc -v
// Version 3.2.2
```
然后我们就新建一个名为 index.ts 的文件，然后敲入简单点的代码：
```
// index.ts
const msg: string = 'Hello TypeScript';
```
代码编写好就可以执行编译，可以运行 tsc 命令，让 ts 文件变成可在浏览器中运行的 js 文件：
```
tsc index.ts
```
如果你的代码不合法，执行 tsc 的时候就会报错，根据错误进行对应的修改即可。

## Hello TypeScript
我们看一个稍微完整点的例子吧。

这是一个 ts 文件，声明了一个 sayHello 函数：

函数有一个入参：string 类型的 name
函数有一个返回值：string 类型的 Hello ${name}
```
// index.ts
function sayHello(name: string): string {
    return`Hello ${name}`;
}

const me: string = 'axuebin';
console.log(sayHello(me))
```
我们执行 tsc index.ts 编译一下，在同级文件夹下生成了一个新的文件 index.js：
```
function sayHello(name) {
    return"Hello " + name;
}
var me = 'axuebin';
console.log(sayHello(me));
```
我们发现我们写的 TypeScript 代码在编译后都消失了。因为 TypeScript 只会进行静态检查，如果代码有问题，在编译阶段就会报错。

我们修改一下 index.ts ，模拟一下出错的情况：
```
function sayHello(name: string): string {
    return`Hello ${name}`;
}

const count: number = 1000;
console.log(sayHello(count))
```
我们向 sayHello 传递一个 number 类型的参数，试试 tsc 一下：
```
index.ts:6:22 - error TS2345: Argument of type 'number' is not assignable to parameter of type 'string'.
```
命令行就会报上面的错误，意思是不能给一个 string 类型的参数传递一个 number 类型。

但是，这里要注意的是，即使报错了，tsc 还是会「将编译进行到底」，还是会生成一个 index.js 文件：
```
function sayHello(name) {
    return"Hello " + name;
}
var count = 1000;
console.log(sayHello(count));
```
看上去也就是没啥毛病的 js 代码。

如果编译失败就不生成 js 文件，之后可以在配置中关闭这个功能。

## 为什么要使用TypeScript?
- 程序更容易理解（函数或者方法输入输出的参数类型，外部条件等）；
- 效率更高，在不同代码块和定义中进行跳转，代码补全等；
- 更少的错误，编译期间能够发现大部分的错误，杜绝一些比较常见的错误；
- 好的包容性，完成兼容 JavaScript，第三方库可以单独编写类型文件；
缺点：
- 增加学习成本；
- 短期内新增了一些开发成本；

## JavaScript 与 TypeScript 的区别
TypeScript 是 JavaScript 的超集，扩展了 JavaScript 的语法，因此现有的 JavaScript 代码可与 TypeScript 一起工作无需任何修改，TypeScript 通过类型注解提供编译时的静态类型检查。

TypeScript 可处理已有的 JavaScript 代码，并只对其中的 TypeScript 代码进行编译。

## TypeScript 的优势
下面列举 TypeScript 相比于 JavaScript 的显著优势：
- 静态输入
静态类型化是一种功能，可以在开发人员编写脚本时检测错误。查找并修复错误是当今开发团队的迫切需求。有了这项功能，就会允许开发人员编写更健壮的代码并对其进行维护，以便使得代码质量更好、更清晰。
- 大型的开发项目
有时为了改进开发项目，需要对代码库进行小的增量更改。这些小小的变化可能会产生严重的、意想不到的后果，因此有必要撤销这些变化。使用TypeScript工具来进行重构更变的容易、快捷。
- 更好的协作
当发开大型项目时，会有许多开发人员，此时乱码和错误的机也会增加。类型安全是一种在编码期间检测错误的功能，而不是在编译项目时检测错误。这为开发团队创建了一个更高效的编码和调试过程。
- 更强的生产力
干净的 ECMAScript 6 代码，自动完成和动态输入等因素有助于提高开发人员的工作效率。这些功能也有助于编译器创建优化的代码。
## JavaScript 的优势
相比于 TypeScript，JavaScript 也有一些明显优势。
- 人气
JavaScript 的开发者社区仍然是巨大而活跃的，在社区中可以很方便地找到大量成熟的开发项目和可用资源。
- 学习曲线
由于 JavaScript 语言发展的较早，也较为成熟，所以仍有一大批开发人员坚持使用他们熟悉的脚本语言 JavaScript，而不是学习 TypeScript。
- 本地浏览器支持
TypeScript 代码需要被编译（输出 JavaScript 代码），这是 TypeScript 代码执行时的一个额外的步骤。
- 不需要注释
为了充分利用 TypeScript 特性，开发人员需要不断注释他们的代码，这可能会使项目效率降低。
- 灵活性
有些开发人员更喜欢 JavaScript 的灵活性。
