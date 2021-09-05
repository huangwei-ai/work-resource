## call、apply、bind
那了解了函数 this 指向的不同场景之后，我们知道有些情况下我们为了使用某种特定环境的 this 引用，
这时候时候我们就需要采用一些特殊手段来处理了，例如我们经常在定时器外部备份 this 引用，然后在定时器函数内部使用外部 this 的引用。
然而实际上对于这种做法我们的 JavaScript 为我们专门提供了一些函数方法用来帮我们更优雅的处理函数内部 this 指向问题。
这就是接下来我们要学习的 call、apply、bind 三个函数方法。

## 一、call
功能: 调用函数,改变函数中的this指向
###参数:
第一个参数 : 设置this的指向,如果指定了 null 或者 undefined 则内部 this 指向 window
其他参数 : 对应函数的参数
call的返回值就是函数的返回值
```
function fn(x,y) {
    console.log(this);  //{name: "cc", age: 16}
    console.log(x+y);    //11
}
var obj ={
    name:'cc',
    age:16
}
//语法:
fn.call(obj,2,9)  
```
### 应用
```
var obj = {
    0:10,
    1:20,
    2:30,
    3:40,
    length:4
};
Array.prototype.push.call(obj,30)
console.log(obj)  //{0: 10, 1: 20, 2: 30, 3: 40, 4: 30, length: 5}
Array.prototype.splice.call(obj,0,2)
console.log(obj)  //{0: 30, 1: 40, 2: 30, length: 3}

var obj1 = {
    name:'cc'
}
console.log(obj1.toString());  //[object Object]
var arr = [2,9];
console.log(arr.toString());    //2,9
console.log(Object.prototype.toString.call(arr));  //[object Array]
```
## 二、apply
调用函数，改变函数中的this
### 参数
第一个参数 : 设置函数内部this的指向

第二个参数 : 是数组

call的返回值就是函数的返回值
```
function fn(x,y) {
    console.log(this)  //Object
    console.log(x+y)  //11
}
var obj = { 
    name:'cc'
}
fn.apply(obj,[2,9])
var arr = [1,2,3,4,5];
console.log(Math.max(arr));  //NaN
console.log(Math.max.apply(Math,arr))  //5
console.log.apply(console,arr) //1 2 3 4 5
```
## 三、bind
bind() 函数会创建一个新函数（称为绑定函数），新函数与被调函数（绑定函数的目标函数）具有相同的函数体（在 ECMAScript 5 规范中内置的call属性）。
当目标函数被调用时 this 值绑定到 bind() 的第一个参数，该参数不能被重写。绑定函数被调用时，bind() 也接受预设的参数提供给原函数。
一个绑定函数也能使用new操作符创建对象：这种行为就像把原函数当成构造器。提供的 this 值被忽略，同时调用时的参数被提供给模拟函数。

语法：
```
fun.bind(thisArg[, arg1[, arg2[, ...]]])
```
### 参数：

thisArg

当绑定函数被调用时，该参数会作为原函数运行时的 this 指向。当使用new 操作符调用绑定函数时，该参数无效。
arg1, arg2, …

当绑定函数被调用时，这些参数将置于实参之前传递给被绑定的方法。
返回值：

返回由指定的this值和初始化参数改造的原函数拷贝。

示例1：
```
this.x = 9; 
var module = {
  x: 81,
  getX: function() { return this.x; }
};

module.getX(); // 返回 81

var retrieveX = module.getX;
retrieveX(); // 返回 9, 在这种情况下，"this"指向全局作用域

// 创建一个新函数，将"this"绑定到module对象
// 新手可能会被全局的x变量和module里的属性x所迷惑
var boundGetX = retrieveX.bind(module);
boundGetX(); // 返回 81
```
示例2：
```
function LateBloomer() {
  this.petalCount = Math.ceil(Math.random() * 12) + 1;
}

// Declare bloom after a delay of 1 second
LateBloomer.prototype.bloom = function() {
  window.setTimeout(this.declare.bind(this), 1000);
};

LateBloomer.prototype.declare = function() {
  console.log('I am a beautiful flower with ' +
    this.petalCount + ' petals!');
};

var flower = new LateBloomer();
flower.bloom();  // 一秒钟后, 调用'declare'方法
```
## 小结
### call 和 apply 特性一样

- 都是用来调用函数，而且是立即调用
- 但是可以在调用函数的同时，通过第一个参数指定函数内部 this 的指向
- call 调用的时候，参数必须以参数列表的形式进行传递，也就是以逗号分隔的方式依次传递即可
- apply 调用的时候，参数必须是一个数组，然后在执行的时候，会将数组内部的元素一个一个拿出来，与形参一一对应进行传递
- 如果第一个参数指定了 null 或者 undefined 则内部 this 指向 window
### bind

- 可以用来指定内部 this 的指向，然后生成一个改变了 this 指向的新的函数
它和 call、apply 最大的区别是：bind 不会调用
- bind 支持传递参数，它的传参方式比较特殊，一共有两个位置可以传递
在 bind 的同时，以参数列表的形式进行传递
- 在调用的时候，以参数列表的形式进行传递
- 那到底以谁 bind 的时候传递的参数为准呢还是以调用的时候传递的参数为准
- 两者合并：bind 的时候传递的参数和调用的时候传递的参数会合并到一起，传递到函数内部
### 简单的说
- call的参数是直接放进去的，第二第三第n个参数全都用逗号分隔，直接放到后面
- apply的所有参数都必须放在一个数组里面传进去
- bind除了返回是函数以外，它 的参数和call 一样(需要用小括号调用函数)
  
————————————————
参考资料
CSDN博主「灯火暗」的原创文章
原文链接：https://blog.csdn.net/denghuocc/article/details/89157308