## 1、变量声明
```
let x; 
let y = 20; 
// 简写
let x, y = 20;
```
## 2、多个变量赋值
我们可以使用数组解构来在一行中给多个变量赋值
```
let a, b, c; 
a = 5; 
b = 8; 
c = 12;
// 简写
let [a, b, c] = [5, 8, 12];
```

## 3、 三元运算符
```
if(a = true){
 console.log(1);
}else{
 console.log(2);
}
// 简写
a = true ?  console.log(1) :  console.log(2);
```
## 4. 赋默认值
我们可以使用 || 运算符来给一个变量赋默认值
```
let imagePath; 
let path = getImagePath(); 
if(path !== null && path !== undefined && path !== '') { 
  imagePath = path; 
} else { 
  imagePath = 'default.jpg'; 
} 
// 简写 
let imagePath = getImagePath() || 'default.jpg';
```

## 5、与运算符 (&&)
如果你只有当某个变量为 true 时调用一个函数，那么你可以使用与 (&&)运算符形式书写
```
if (isLoggedin) {
 goToHomepage(); 
} 
// 简写
isLoggedin && goToHomepage();
```

## 6、交换两个变量
为了交换两个变量，我们通常使用第三个变量。但是我们也可以使用数组解构赋值来交换两个变量。
```
let x = 'Hello', y = "javaScript"; 
const temp = x; 
x = y; 
y = temp; 
// 简写
[x, y] = [y, x];
```

## 7、箭头函数
```
function add(num1, num2) { 
   return num1 + num2; 
} 
// 简写
const add = (num1, num2) => num1 + num2;
```

## 8、模板字符串
我们一般使用 + 运算符来连接字符串变量。使用 ES6 的模板字符串，我们可以用一种更简单的方法实现这一点。
```
console.log('You got a missed call from ' + number + ' at ' + time); 
// 简写
console.log(`You got a missed call from ${number} at ${time}`);
```

## 9、多条件检查
对于多个值匹配，我们可以将所有的值放到数组中，然后使用indexOf()或includes()方法。
```
if (value === 1 || value === 'one' || value === 2 || value === 'two') { 
     // Execute some code 
} 
// 简写方法 1
if ([1, 'one', 2, 'two'].indexOf(value) >= 0) { // indexOf 返回下标 没有则返回-1
    // Execute some code 
}
// 简写方法 2
if ([1, 'one', 2, 'two'].includes(value)) { // includes 返回 turn / false
    // Execute some code 
}
```

## 10、对象属性复制
如果变量名和对象的属性名相同，那么我们只需要在对象语句中声明变量名，而不是同时声明键和值。JavaScript 会自动将键作为变量的名，将值作为变量的值。
```
let firstname = 'Amitav'; 
let lastname = 'Mishra'; 
let obj = {firstname: firstname, lastname: lastname}; 
// 简写
let obj = {firstname, lastname}; // es6中的速写属性
```

## 11、字符串转换成数字
除了使用内置属性parseInt和parseFloat可以用来将字符串转为数字。我们还可以简单地在字符串前提供一个一元运算符 (+) 来实现这一点。
```
let total = parseInt('453');  // 整型数字
let average = parseFloat('42.6');  // 浮点型数字 即 带小数点的数
// 简写
let total = +'453'; 
let average = +'42.6';
```

## 12、重复一个字符串多次
为了重复一个字符串 N 次，你可以使用for循环。但是使用repeat()方法，我们可以一行代码就搞定。
```
let str = ''; 
for(let i = 0; i < 5; i ++) { 
  str += 'Hello '; 
} 
console.log(str); // Hello Hello Hello Hello Hello 
// 简写 
'Hello'.repeat(5);
```
## 13、优雅的获取数组中的最大值 同理也可以求得最小值
我们可以使用 for 循环来遍历数组中的每一个值，然后找出最大或最小值。我们还可以使用 Array.reduce() 方法来找出数组中的最大和最小数字。但是使用扩展符号，我们一行就可以实现。
```
var arr1 = [1,2,3,4,999,1999];
Math.max(...arr); // 1999
Math.min(...arr); // 1
```

## 14、指数幂
我们可以使用Math.pow()方法来得到一个数字的幂。有一种更短的语法来实现，即双星号（ ** ）
```
const power = Math.pow(4, 3); // 64 
// Shorthand 
const power = 4**3; // 64
```

## 15、双非位运算符 (~~)
双非位运算符是Math.floor()方法的缩写。
```
const floor = Math.floor(6.8); // 6 
// 简写
const floor = ~~6.8; // 6
```
双非位运算符只对 32 位整数有效，例如 (2 ** 31)-1 = 2147483647。所以对于任何大于 2147483647 的数字，双非位运算符 (~~) 都会给出错误的结果，这种情况下推荐使用 Math.floor() 方法

## 16. 声明和初始化数组
我们可以使用默认值（如""、null或 ）初始化特定大小的数组0。您可能已经将这些用于一维数组，但如何初始化二维数组/矩阵呢？
```
const array = Array(5).fill(''); 
// 输出
(5) ["", "", "", "", ""]

const matrix = Array(5).fill(0).map(()=>Array(5).fill(0)); 
// 输出
(5) [Array(5), Array(5), Array(5), Array(5), Array(5)]
0: (5) [0, 0, 0, 0, 0]
1: (5) [0, 0, 0, 0, 0]
2: (5) [0, 0, 0, 0, 0]
3: (5) [0, 0, 0, 0, 0]
4: (5) [0, 0, 0, 0, 0]
length: 5
```

## 17. 找出总和、最小值和最大值
我们应该利用reduce方法来快速找到基本的数学运算。
const array  = [5,4,7,8,9,2];
```
array.reduce((a,b) => a+b);
// 输出: 35
```
最大限度
```
array.reduce((a,b) => a>b?a:b);
// 输出: 9
```
最小
```
array.reduce((a,b) => a<b?a:b);
// 输出: 2
```

## 18. 对字符串、数字或对象数组进行排序
我们有内置的方法sort()和reverse()用于对字符串进行排序，但是数字或对象数组呢？
让我们看看数字和对象的升序和降序排序技巧。

排序字符串数组
```
const stringArr = ["Joe", "Kapil", "Steve", "Musk"]
stringArr.sort();
// 输出
(4) ["Joe", "Kapil", "Musk", "Steve"]

stringArr.reverse();
// 输出
(4) ["Steve", "Musk", "Kapil", "Joe"]
```

排序数字数组
```
const array  = [40, 100, 1, 5, 25, 10];
array.sort((a,b) => a-b);
// 输出
(6) [1, 5, 10, 25, 40, 100]

array.sort((a,b) => b-a);
// 输出
(6) [100, 40, 25, 10, 5, 1]
```

对象数组排序
```
const objectArr = [ 
    { first_name: 'Lazslo', last_name: 'Jamf'     },
    { first_name: 'Pig',    last_name: 'Bodine'   },
    { first_name: 'Pirate', last_name: 'Prentice' }
];
objectArr.sort((a, b) => a.last_name.localeCompare(b.last_name));
// 输出
(3) [{…}, {…}, {…}]
0: {first_name: "Pig", last_name: "Bodine"}
1: {first_name: "Lazslo", last_name: "Jamf"}
2: {first_name: "Pirate", last_name: "Prentice"}
length: 3
```
## 19. 从数组中过滤出虚假值
Falsy值喜欢0，undefined，null，false，""，''可以很容易地通过以下方法省略
```
const array = [3, 0, 6, 7, '', false];
array.filter(Boolean);
// 输出
(3) [3, 6, 7]
```

## 20. 对各种条件使用逻辑运算符
如果你想减少嵌套 if…else 或 switch case，你可以简单地使用基本的逻辑运算符AND/OR。
```
function doSomething(arg1){ 
    arg1 = arg1 || 10; 
// 如果尚未设置，则将 arg1 设置为 10 作为默认值
return arg1;
}

let foo = 10;  
foo === 10 && doSomething(); 
// is the same thing as if (foo == 10) then doSomething();
// 输出: 10

foo === 5 || doSomething();
// is the same thing as if (foo != 5) then doSomething();
// 输出: 10
```

## 21. 删除重复值
您可能已经将 indexOf() 与 for 循环一起使用，该循环返回第一个找到的索引或较新的 includes() 从数组中返回布尔值 true/false 以找出/删除重复项。 这是我们有两种更快的方法。
```
const array  = [5,4,7,8,9,2,7,5];
array.filter((item,idx,arr) => arr.indexOf(item) === idx);
// or
const nonUnique = [...new Set(array)];
// 输出: [5, 4, 7, 8, 9, 2]
```

## 22. 创建计数器对象或映射
大多数情况下，需要通过创建计数器对象或映射来解决问题，该对象或映射将变量作为键进行跟踪，并将其频率/出现次数作为值进行跟踪。
```
let string = 'kapilalipak';

const table={}; 
for(let char of string) {
  table[char]=table[char]+1 || 1;
}
// 输出
{k: 2, a: 3, p: 2, i: 2, l: 2}
```
和
```
const countMap = new Map();
  for (let i = 0; i < string.length; i++) {
    if (countMap.has(string[i])) {
      countMap.set(string[i], countMap.get(string[i]) + 1);
    } else {
      countMap.set(string[i], 1);
    }
  }
// 输出
Map(5) {"k" => 2, "a" => 3, "p" => 2, "i" => 2, "l" => 2}
```

## 23. 三元运算符很酷
您可以避免使用三元运算符嵌套条件 if…elseif…elseif。
```
function Fever(temp) {
    return temp > 97 ? 'Visit Doctor!'
      : temp < 97 ? 'Go Out and Play!!'
      : temp === 97 ? 'Take Some Rest!';
}

// 输出
Fever(97): "Take Some Rest!" 
Fever(100): "Visit Doctor!"
```

## 24. 与旧版相比，for 循环更快

for并for..in默认为您提供索引，但您可以使用 arr[index]。
for..in 也接受非数字，所以避免它。
forEach,for...of直接获取元素。
forEach也可以为您提供索引，但for...of不能。
for并for...of考虑阵列中的孔，但其他 2 个不考虑。

## 25.合并2个对象
通常我们需要在日常任务中合并多个对象。
```
const user = { 
 name: 'Kapil Raghuwanshi', 
 gender: 'Male' 
 };
const college = { 
 primary: 'Mani Primary School', 
 secondary: 'Lass Secondary School' 
 };
const skills = { 
 programming: 'Extreme', 
 swimming: 'Average', 
 sleeping: 'Pro' 
 };

const summary = {...user, ...college, ...skills};

// 输出
gender: "Male"
name: "Kapil Raghuwanshi"
primary: "Mani Primary School"
programming: "Extreme"
secondary: "Lass Secondary School"
sleeping: "Pro"
swimming: "Average"
```

## 26. 箭头函数
箭头函数表达式是传统函数表达式的紧凑替代品，但有局限性，不能在所有情况下使用。由于它们具有词法范围（父范围）并且没有自己的范围this，arguments因此它们指的是定义它们的环境。
```
const person = {
name: 'Kapil',
sayName() {
    return this.name;
    }
}
person.sayName();
// 输出
"Kapil"
```
但
```
const person = {
name: 'Kapil',
sayName : () => {
    return this.name;
    }
}
person.sayName();
// 输出
""
```

## 27. 可选链
可选的链接 ?.如果值在 ? 之前，则停止评估。为 undefined 或 null 并返回
undefined。
```
const user = {
  employee: {
    name: "Kapil"
  }
};
user.employee?.name;
// 输出: "Kapil"
user.employ?.name;
// 输出: undefined
user.employ.name
// 输出: VM21616:1 Uncaught TypeError: Cannot read property 'name' of undefined
```

## 28. 打乱数组
利用内置Math.random()方法。
```
const list = [1, 2, 3, 4, 5, 6, 7, 8, 9];
list.sort(() => {
    return Math.random() - 0.5;
});
// 输出
(9) [2, 5, 1, 6, 9, 8, 4, 3, 7]
// Call it again
(9) [4, 1, 7, 5, 3, 8, 2, 9, 6]
```

## 29. 空合并算子
空合并运算符 (??) 是一个逻辑运算符，当其*左侧操作数为空或未定义时*返回其右侧操作数，否则返回其左侧操作数。
```
const foo = null ?? 'my school';
// 输出: "my school"

const baz = 0 ?? 42;
// 输出: 0
```

## 30. Rest & Spread 运算符
那些神秘的3点...可以休息或传播！🤓
```
function myFun(a,  b, ...manyMoreArgs) {
   return arguments.length;
}
myFun("one", "two", "three", "four", "five", "six");

// 输出: 6
```
和
```
const parts = ['shoulders', 'knees']; 
const lyrics = ['head', ...parts, 'and', 'toes']; 

lyrics;
// 输出: 
(5) ["head", "shoulders", "knees", "and", "toes"]
```

## 31. 默认参数
```
const search = (arr, low=0,high=arr.length-1) => {
    return high;
}
search([1,2,3,4,5]);

// 输出: 4
```

## 32. 将十进制转换为二进制或十六进制
在解决问题的同时，我们可以使用一些内置的方法，例如.toPrecision()或.toFixed()来实现许多帮助功能。
```
const num = 10;

num.toString(2);
// 输出: "1010"
num.toString(16);
// 输出: "a"
num.toString(8);
// 输出: "12"
```
## 33. 使用解构简单交换两值
```
let a = 5;
let b = 8;
[a,b] = [b,a]

[a,b]
// 输出
(2) [8, 5]
```

## 34. 单行回文检查
嗯，这不是一个整体的速记技巧，但它会让你清楚地了解如何使用弦乐。
```
function checkPalindrome(str) {
  return str == str.split('').reverse().join('');
}
checkPalindrome('naman');
// 输出: true
```

## 35.将Object属性转成属性数组
使用Object.entries(),Object.keys()和Object.values()
```
const obj = { a: 1, b: 2, c: 3 };

Object.entries(obj);
// 输出
(3) [Array(2), Array(2), Array(2)]
0: (2) ["a", 1]
1: (2) ["b", 2]
2: (2) ["c", 3]
length: 3

Object.keys(obj);
(3) ["a", "b", "c"]

Object.values(obj);
(3) [1, 2, 3]
```

## 一、数据类型检测
### .1 typeof
typeof操作符返回一个字符串，表示未经计算的操作数的类型；该运算符数据类型（返回字符串，对应列表如图）
![image](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/3/8/1695b063b387ac39~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

### 1.2 instanceof
```
var str = "This is a simple string"; 
var num = 1111;
var boolean = true;
var und = undefined;
var nl = null;
var sb = Symbol('1111');
var obj = {}; // 非原始类型数据字面量定义
console.log(str instanceof String);         // false
console.log(num instanceof Number);         // false
console.log(boolean instanceof Boolean);    // false
console.log(nl instanceof Object);          // false
console.log(sb instanceof Symbol);          // false
console.log(obj instanceof Object);         // true
var strN = new String("This is a simple string");
var numN = new Number(1111);
var booleanN = new Boolean(true);
var objN = new Object();
```
```
console.log(strN instanceof String);            // true
console.log(numN instanceof Number);            // true
console.log(booleanN instanceof Boolean);       // true
console.log(objN instanceof Object);            // true
```
instanceof运算符用于测试构造函数的prototype属性是否出现在对象的原型链中的任何位置；
由上结果，字面量产出的原始数据类型无法使用instanceof判断。

### 1.3 Object.propotype.toString
```
Object.prototype.toString.call('string');       //"[object String]"
Object.prototype.toString.call(1111);           //"[object Number]"
Object.prototype.toString.call(true);           //"[object Boolean]"
Object.prototype.toString.call(null);           //"[object Null]"
Object.prototype.toString.call(undefined);      //"[object Undefined]"
Object.prototype.toString.call(Symbol('111'));  //"[object Symbol]"
Object.prototype.toString.call({});             //"[object Object]"
```
上述方法最为便捷有效

### 1.4 constructor
比较对象的构造函数与类的构造函数是否相等
```
var a = {}
a.constructor === Object     // true
var b = '111';
b.constructor === String    // true
var strN = new String('11111');
strN.constructor === String // true
var c = true;
c.constructor === Boolean   // true
```
```
var d = Symbol('symbol')
d.constructor === Symbol    // true
```

### 1.5 propotype
比较对象的原型与构造函数的原型是否相等
```
var a = {}
a.__proto__ === Object.prototype     // true
var t = new Date();
t.proto === Date.prototype      // true
var str = 'sting';
str.proto === String.prototype  // true
var strN = new String('11111');
strN.proto === String.prototype // true
```

## 二、数据特殊操作
### 2.1 交换两个值
#### 2.1.1 利用一个数异或本身等于0和异或运算符合交换率
```
var a = 3;
var b = 4
a ^= b; // a = a ^ b
b ^= a;
a ^= b;
```
```
console.log(a, b);
```
#### 2.1.2 使用ES6解构赋值
```
let a = 1;
let b = 2;

[b, a] = [a, b];

console.log(a, b);
```
### 2.2 小数取整
```
var num = 123.123

// 常用方法
console.log(parseInt(num)); // 123
// “双按位非”操作符
console.log(~~ num); // 123
// 按位或
console.log(num | 0); // 123
// 按位异或
console.log(num ^ 0); // 123
// 左移操作符
console.log(num << 0); // 123
```

### 2.3 数字金额千分位格式化
#### 2.3.1 使用Number.prototype.toLocaleString()
```
var num = 123455678;
var num1 = 123455678.12345;
var formatNum = num.toLocaleString('en-US');
var formatNum1 = num1.toLocaleString('en-US');
复制代码console.log(formatNum); // 123,455,678
console.log(formatNum1); // 123,455,678.123
复制代码2.3.2 使用正则表达式
var num = 123455678;
var num1 = 123455678.12345;

var formatNum = String(num).replace(/\B(?=(\d{3})+(?!\d))/g, ',');
var formatNum1 = String(num1).replace(/\B(?=(\d{3})+(?!\d))/g, ',');

console.log(formatNum); // 123,455,678
console.log(formatNum1); // 123,455,678.12,345
```

## 三、对象数据常用操作
### 3.1 深度克隆技巧
#### 3.1.1 JSON.stringify 转换成字符， JSON.parse重新生成JSON数据类型
```
function deepClone(obj) {
    return JSON.parse(JSON.stringify(obj));
}
var obj = {
    number: 1,
    string: 'abc',
    bool: true,
    undefined: undefined,
    null: null,
    symbol: Symbol('s'),
    arr: [1, 2, 3],
    date: new Date(),
    userInfo: {
        name: 'Better',
        position: 'front-end engineer',
        skill: ['React', 'Vue', 'Angular', 'Nodejs', 'mini programs']
    },
    func: function () {
        console.log('hello better');
    }
}
```
```
console.log(deepClone(obj))
```
从打印结果可以得出以下结论：
undefined、symbol、function 类型直接被过滤掉了
date 类型被自动转成了字符串类型

#### 3.1.2 常用方式 简单递归
```
function deepClone(obj) {
    var newObj = obj instanceof Array ? [] : {};
    for (let i in obj) {
        newObj[i] = typeof obj[i] === 'object' ? deepClone(obj[i]) : obj[i]
    }
    return newObj;
}
var obj = {
number: 1,
string: 'abc',
bool: true,
undefined: undefined,
null: null,
symbol: Symbol('s'),
arr: [1, 2, 3],
date: new Date(),
userInfo: {
name: 'Better',
position: 'front-end engineer',
skill: ['React', 'Vue', 'Angular', 'Nodejs', 'mini programs']
},
func: function () {
console.log('hello better');
}
}
```
```
console.log(deepClone(obj))
```
从打印的结果来看，这种实现方式还存在很多问题：这种方式只能实现特定的object的深度复制（比如对象、数组和函数），不能实现null以及包装对象Number，String ，Boolean，以及Date对象，RegExp对象的复制。

### 3.2 对象遍历方式
#### 3.2.1 for-in
```
function A() {
	this.a = 1
	this.b = 1
}
A.prototype = {
c: 1,
d: 2
}
var a = new A()`
```
```
for(var i in a) {
console.log(i)
}
```
由上打印结果可知，for-in 会遍历对象属性，包括原型链中的属性

#### 3.2.2 Object.entries()
```
function A() {
	this.a = 1
	this.b = 1
}
A.prototype = {
c: 1,
d: 2
}
```
```
var a = new A()
var et = Object.entries(a)
console.log(et)
```
由上结果可知，entries 返回一个给定对象自身可枚举属性的键值对数组

#### 3.2.3 Object.keys()、 Object.values()
```
function A() {
	this.a = 1
	this.b = 1
}
A.prototype = {
c: 1,
d: 2
}
```
```
var a = new A()
var keys = Object.keys(a)
var values = Object.values(a)
console.log(keys, values)
```
由上结果可知，keys, values 返回一个给定对象自身可枚举属性数组,自身可枚举属性值的数组

## 四、数组常用操作
### 4.1 数组去重
#### 4.1.1 Set 去重
```
var arr = [1,2,1,1,22,4,5,6];
arr1 = [...new Set(arr)];
```

#### 4.1.2 结合使用数组filter方法和indexOf()方法
```
var arr = [1, 2, 3, 2, 6, '2', 3, 1];
function uniqueArr (arr) {
    return arr.filter(function (ele, index, array) {
        // 利用数组indexOf()方法，返回找到的第一个值的索引
        // 如果数组元素的索引值与indexOf方法查找返回的值不相等，则说明该值重复了，直接过滤掉
        return array.indexOf(ele) === index;
    })
}
```
## 4.2 多维数组一行代码实现一维转换
```
var arr = [ [1, 2, 2], [3, 4, 5, 5], [6, 7, 8, 9, [11, 12, [12, 13, [14] ] ] ], 10];
var resultArr = arr.toString().split(',').map(Number);
```
```
console.log(resultArr); // [1, 2, 2, 3, 4, 5, 5, 6, 7, 8, 9, 11, 12, 12, 13, 14, 10]
```

## 4.3 一行代码实现获取一个网页使用了多少种标签
```
[...new Set([...document.querySelectorAll('*')].map(node => node.tagName))].length;
```

## 4.4 如何实现a == 1 && a == 2 && a == 3
### 4.4.1  改写数组的toString方法
```
var a = [1, 2, 3];
// a.join = a.shift;
// a.valueOf = a.shift;
a.toString = a.shift;
```
```
console.log(a == 1 && a == 2 && a == 3); // true
```
原理：当复杂类型数据与基本类型数据作比较时会发生隐性转换，会调用toString()或者valueOf()方法
#### 4.4.2  改写对象的toString方法
```
var a = {
    value: 1,
    toString: function () {
        return a.value++;
    }
}
console.log(a == 1 && a == 2 && a == 3); // true
```
### 4.5 统计字符串中相同字符出现的次数
```
var str = 'aaabbbccc66aabbc6';
var strInfo = str.split('').reduce((p, c) => (p[c]++ || (p[c] = 1), p), {});
```
```
console.log(strInfo); // {6: 3, a: 5, b: 5, c: 4}
```

### 4.6 将类数组对象转成数组
#### 4.6.1 使用Array.prototype.slice
```
var likeArrObj = {
    0: 1,
    1: 2,
    2: 3,
    length: 3
}
```
```
var arr1 = Array.prototype.slice.call(likeArrObj); // 或者使用[].slice.call(likeArrObj);
console.log(arr1); // [1, 2, 3]
```
#### 4.6.2 使用Array.from
```
var likeArrObj = {
    0: 1,
    1: 2,
    2: 3,
    length: 3
}

var arr = Array.from(likeArrObj);
console.log(arr); // [1, 2, 3]
```
#### 4.6.3 使用Object.values (此处省略)