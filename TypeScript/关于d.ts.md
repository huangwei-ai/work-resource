## 一.简介
经常看到d.ts，因为一个越来越广泛的应用场景是**编辑器智能提示**（具体见IntelliSense based on TypeScript Declaration Files）：

>JavaScript IntelliSense can be provided for values declared in a .d.ts file (more info), and types such as interfaces and classes declared in TypeScript are available for use as types in JsDoc comments.

d.ts大名叫TypeScript Declaration File，存放一些声明，类似于C/C++的.h头文件（#include <stdio.h>）：

>We call declarations that don’t define an implementation “ambient”. Typically these are defined in .d.ts files. If you’re familiar with C/C++, you can think of these as .h files.

（摘自Working with Other JavaScript Libraries）

P.S.另一个“ambient”（环境？）相关的概念Ambient Namespace，指的也是只有声明没有实现的namespace

## 二.分类
声明文件本身没有类别，但不同类型的类库在API暴露方式等方面存在差异，对应的声明文件也有所区别

例如jQuery 1.x通常以global库的方式引用：
```
<script type="text/javascript" src="script/jquery.min.js"></script><script>
  $( "button.continue" ).html( "Next Step..." )</script>
```
而jQuery 3.x支持模块引用：
```
// ES Moduleimport $ from "jquery";// Commonjs Moduleconst $ = require("jquery");
```
从声明文件上看，前者需要声明全局变量jQuery和$，而后者并不默认暴露这些，所以jQuery-1.x.d.ts：
```
declare var jQuery: JQueryStatic;
declare var $: JQueryStatic;
jQuery-3.x.d.ts：

export = jQuery;
```
因此，我们把类库分为3类：

- global：暴露出全局变量的类库

- module：不暴露全局变量，需要通过特定加载机制（如require/define/import）引用的模块形式的类库

- plugin：会影响其它类库功能的类库（当然，也可能会影响原声明，比如添个新API）

3种类库对应的声明文件细分成6种，模板及适用场景如下：

- global.d.ts：适用于global类库

- module-function.d.ts：适用于暴露出一个Function的module类库

- module-class.d.ts：适用于暴露出一个Class的module类库

- module.d.ts：适用于一般module类库（暴露出的东西既不是Function也不是Class）

- module-plugin.d.ts：适用于module plugin类库（A module plugin changes the shape of another module.）

- global-plugin.d.ts：适用于global plugin类库（A global plugin is global code that changes the shape of some global.）

- global-modifying-module.d.ts：适用于module形式的global plugin类库（They’re similar to global plugins, but need a require call to activate their effects.）

P.S.另外，声明文件也存在全局声明冲突的问题，建议通过namespace解决

## 三.引用方式
不同类型的声明文件对应的引用方式也不同，global类库声明通过 `<reference types="..." />`指令引用，例如：
```
<reference types="someLib" />
function getThing(): someLib.thing;
```
module类库声明用import语句引用：
```
import * as moment from "moment";function getThing(): moment;
```
特殊的，UMD类库既可以作为module类库也可以作为global类库引入，此时引用方式取决于当前环境。例如global类库依赖UMD类库的话，仍通过reference指令来引用：
```
/// <reference types="moment" />function getThing(): moment;
```
而module/UMD类库依赖UMD类库时则用import语句：
```
import * as someLib from 'someLib';
```
P.S.关于声明文件引用方式的更多信息，请查看

## 四.语法格式
### 全局变量
```
/** The number of widgets present */
declare var foo: number;
```
declare var声明了一个数值类型的全局变量foo，同样，还有declare const和declare let分别表示只读，及块级作用域

类型部分与TS基本类型语法一致，具体见Basic Types

### 全局函数
```
declare function greet(greeting: string): void;
```
declare function声明了一个函数greet，它接受1个字符串类型参数greeting，返回undefined或null

### 全局对象
```
declare namespace myLib {
    function makeGreeting(s: string): string;
    let numberOfGreetings: number;}
```
declare namespace声明了一个对象myLib，身上有个方法makeGreeting接受1个字符串类型参数s，返回字符串，还有个数值类型的属性numberOfGreetings

### 函数重载
```
declare function getWidget(n: number): Widget;
declare function getWidget(s: string): Widget[];
```
getWidget函数有两个签名，其一接受1个数值类型参数n，返回Widget，其二接受1个字符串类型参数s，返回Widget数组

### 接口
```
interface GreetingSettings {
  greeting: string;
  duration?: number;
  color?: string;}
declare function greet(setting: GreetingSettings): void;
```
interface是一种可复用的类型，上例声明了GreetingSettings的结构，要求参数setting具有greeting以及可选的duration和color属性，类型分别为字符串、数值、字符串

### 类型别名
```
type GreetingLike = string | (() => string) | MyGreeter;
declare function greet(g: GreetingLike): void;
```
type也是一种可复用类型，上例声明了类型别名GreetingLike，要求参数g是字符串或返回字符串的函数或MyGreeter实例

### 类型“模块”
```
declare namespace GreetingLib {
    interface LogOptions {
        verbose?: boolean;
    }
    interface AlertOptions {
        modal: boolean;
        title?: string;
        color?: string;
    }}
```
类似于namespace能够组织代码模块（把一组相关代码放在一起），declare namespace能用来组织类型“模块”（把一组相关类型声明放在一起）

>P.S.同样，嵌套namespace也是支持的，如declare namespace GreetingLib.Options {/…/}

### 类
```
declare class Greeter {
    constructor(greeting: string);
    greeting: string;
    showGreeting(): void;}
```
declare class声明了一个类Greeter，拥有构造函数和成员变量greeting以及成员方法showGreeting

## 五.实践规范
除了遵循基本的语法格式外，实践中还应该遵守这些规范约束：

- 用基础类型（number, string, boolean, object），不要用包装类型（Number, String, Boolean, Object）

- 不要出现未使用的泛型参数，会导致类型无法正确推断

- 无返回值的callback参数返回类型用void，不要用any

- callback的可选参数没必要在类型上标出来，因为callback允许少传/不传参数

- 函数重载需要注意声明顺序，应该从特殊到一般自上而下排列（例如any会短路其它重载声明，类似于模式匹配的机制）

- 能用可选参数（如two?: string）描述的就别用函数重载了

- 能用组合类型（如b: number|string）描述的就别用函数重载了

## 六.类型，值和命名空间
实际上，类型，值和命名空间，这3个基本概念构成了TS灵活多样的类型系统

具体的，类型有5种声明方式：
```
// 类型别名
type sn = number | string;// 接口interface I { x: number[]; }// 类class C { }// 枚举enum E { A, B, C }// 类型引用import * as m from 'someModule';
```
值有6种声明方式：
```
// 变量let, const, var// 模块namespace, module// 枚举enum// 类class// 值引用import// 函数function
```
命名空间可以用来组织类型，例如let x: A.B.C表示变量x的类型是来自A.B命名空间下的C

发现class、enum、import具有双重含义，没错，它们既声明值也提供类型，于是出现了一些有意思的事情：
```
// 值与类型的结合export var Bar: { a: Bar };export interface Bar {
  count: number;}// 类型与类型的结合interface Foo {
  x: number;}class Foo {
  y: number;}// ... elsewhere ...interface Foo {
  z: number;}let a: Foo = ...;
console.log(a.x + a.y + a.z); 

// OK// 命名空间与类型的结合class C {}

// ... elsewhere ...namespace C {
  export let x: number;}let y = C.x; 

// OK// 命名空间与命名空间的结合namespace X {
  export interface Y { }
  export class Z { }}// ... elsewhere ...namespace X {
  export var Y: number;
  export namespace Z {
    export class C { }
  }}
type X = string;
```
基本原则是，只要不冲突就是合法的，具体而言，相同命名空间下的同名值存在冲突，同名类型别名存在冲突，而命名空间不会和其它东西冲突：

>Values always conflict with other values of the same name unless they are declared as namespaces, types will conflict if they are declared with a type alias declaration (type s = string), and namespaces never conflict.

所以上例中的某些命名（Bar、Foo）虽然存在多种含义，但都不冲突，仍然是合法的

## 七.自动生成
### dts-gen（不建议用）
#### 全局安装dts-gen
```
npm install -g dts-gen
```
Microsoft/dts-gen是官方脚手架工具（已经1年不更新了，但聊胜于无），能为JS生成d.ts：

>dts-gen is a tool that generates TypeScript definition files (.d.ts) from any JavaScript object.

能够帮助生成基本的API列表，例如：
```
export function containsEmoji(...args: any[]): any;export function isEmoji(...args: any[]): any;export namespace containsEmoji {
    const prototype: {
    };}export namespace isEmoji {
    const prototype: {
    };}
```
具体用法如下：
```
// 从npm模块生成
dts-gen -m emoutils
// 从本地文件生成
dts-gen -e "require('/absolute-path-to/emoutils.js')"
```
P.S.require本地文件要写绝对路径，否则无法正确加载，具体见

>expression-file doesn’t seem to work; ‘Couldn’t load module “undefined”‘

用法有些奇怪，因为这个东西是运行时的：

>It simply examines the objects as they appear at runtime, rather than needing the source code that creates the object.

所以，得到的API列表肯定全，但参数类型、JSDoc等就无能为力了，算是一种取舍：

>This means no matter how the object was written, anything, including native objects, can be given an inferred shape.

代价是生成产物缺少很多源码相关的静态信息：

>Keep in mind dts-gen doesn’t figure out everything – for example, it typically substitutes parameter and return values as any, and can’t figure out which parameters are optional.

### dts-gen生成的东西太弱了，那么，有没有更厉害的方式？

有。TypeScript编译源码时本来就会推断校验参数类型，函数签名等，这些信息输出出来就是d.ts：

>When a TypeScript script gets compiled there is an option to generate a declaration file (with the extension .d.ts) that functions as an interface to the components in the compiled JavaScript.

（摘自Declaration files）

### tsc（推荐）
安装：
```
# 全局安装typescript
npm install typescript -g
# 测试安装是否成功
tsc --version
```
使用：
```
# 后缀名改为.ts
cp ./path-to/my-file.js ./my-file.ts
# 从.ts生成d.ts
tsc --declaration my-file.ts
```
仅支持TS文件，—allowJs选项在这里不可用（更多相关信息见Allow —declaration with —allowJs）：

>error TS5053: Option ‘allowJs’ cannot be specified with option ‘declaration’.

静态语义分析比运行时强大很多，能够推断参数类型、识别JSDoc，生成结果如下：
```
/**
* 是不是一个emoji
* @param {String} str
*/
declare function isEmoji(str?: string): boolean;/**
* 是否含有emoji
* @param {String}} str
*/
declare function containsEmoji(str?: string): boolean;
declare var emoutils: {
    isEmoji: (str?: string) => boolean;
    containsEmoji: (str?: string) => boolean;
    str2unicodeArray: (str?: string) => string[];
    length: (str?: string) => number;
    substr: (str?: string, start?: number, len?: number) => string;
    toArray: (str?: string) => any[];};export default emoutils;

export { isEmoji, containsEmoji, str2unicodeArray, length, substr, toArray };
```
相当完美，所以推荐使用这种方式，唯一缺点是要改后缀名，可以参考How do I rename the extension for a batch of files?

## 八.发布
经常看到类似@types/xxx的npm模块，其实它们都来自DefinitelyTyped/DefinitelyTyped

当然，也可以把自己模块的API声明放上去，具体见How can I contribute?以及Microsoft/types-publisher工具

除了发布独立的typings模块，还可以随功能模块一起发，有两种方式：

- index.d.ts：把index.d.ts放在模块根目录下发布出去

- 指定types/typings：在package.json里添上types（或者typings）字段，例如”types”: “./lib/main.d.ts”

但types/typings都是非npm标准字段，所以建议使用第一种方式

### 安装
如果依赖的功能模块没附带types，可以通过TypeSearch搜索想要的typings模块，再单独安装，例如：
```
npm install --save @types/lodash
```
像功能模块一样正常引用即可：
```
import * as _ from "lodash";
_.padStart("Hello TypeScript!", 20, " ");
```
----
参考资料
TypeScript Declaration File

Writing Declaration Files for @types

Declaration file

关于本文
作者：@ayqy
原文：http://blog.ayqy.net/