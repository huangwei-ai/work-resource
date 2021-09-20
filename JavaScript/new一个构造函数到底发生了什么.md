## 一、对象直接量
---

概要：什么是对象，什么是对象直接量写法，空对象不是真正的空对象

我们可以将js中的对象简单理解为名值对组成的散列表，其中值都是名的属性，值可以是原始值(string,number...)，也可以是对象，而当对象是函数时，我们一般称之为方法。

js中自定义的对象任何时候都是可变的，内置本地对象的属性也是可以修改的，比如你可以先创建一个空对象，然后在给它添加一些属性或方法。而在创建对象时，对象直接量写法是较为理想的方式。

```
var me = {};
me.name = "echo";
me.getName = function () {
  return me.name;
};
```
你可以删除对象的某个属性或方法
```
delete me.name;
```
其实我们创建一个对象也没必要像上面先创建空对象，再一步步添加属性方式，在对象创建时可以同时把你需要定义的属性同时添加好。
```
let me = {
  name = "echo",
  getName = function () {
    return this.name;
  }
};
```
那么这种直接用 = 创建对象的方式就是对象直接量的写法，很直接不是吗。对象直接量语法包括：

• 将对象主体包含在一对花括号内。
• 对象内的属性或方法之间使用逗号分隔。最后一个名值对后也可以有逗号，但 在IE下会报错，所以尽量不要在最后一个属性或方法后加逗号。
• 属性名和值之间使用冒号分隔
• 如果将对象赋值给一个变量，不要忘了在右括号之后补上分号

我在上方提到的空对象可以说是算是一种简称，它们并不是真正的空对象，即便申明一个{}，它也会从Object.prototype继承很多属性和方法，但是我们其实有方法创建严格意义上的空对象，我在上一篇文章中就列出了两种方法，有兴趣可以去看看。

## 二、通过构造函数创建对象

概要：为什么推荐对象直接量写法而不是构造函数写法

尽管JS中没有类的概念，但当你想快速创建多个具有共同特征的实例时还是可以使用构造函数，JS中内置了不少构造函数，例如Object()，Date()，String()等等。

我们用构造函数的写法来创造上面的对象：
```
var me = new Object();
me.name = "echo";
me.getName = function () {
  return me.name;
}
```
相比之下，对象直接量的写法与构造函数写法相比代码更少，推荐直接量写法的还有两个原因

- 一是它可以强调对象是一个简单的可变的散列表，而不必一定派生自某个类。

- 二是当你使用Object()创建对象时，解析器需要顺着作用域链开始查找，直到找到Object构造函数为止，而直接量的写法是不会存在作用域解析行为。

 ## 四、自定义构造函数

概要：当你new一个构造函数时发生了什么？

除了对象直接量和内置构造函数之外，我们还可以通过自定义的构造函数来创建实例对象，像这样。

```
var Person = function () {
  this.name = "echo";
  this.sayName = function () {
    console.log('my name is '+ this.name);
  };
}
var me = new Person();
me.sayName();//my name is echo
```
自定义的构造函数名Person的字母P其实可以小写，但我们都知道，内置构造函数都是大写开头，所以为了让构造函数更为醒目，推荐首字母大写！

很奇怪对不对，我们new一个构造函数得到一个实例，这个实例就继承了构造函数的属性方法，那new这个过程中到底发生了什么？

- <b>1.创建一个空对象，将它的引用赋给this，继承函数的原型。

- 2.通过this将属性和方法添加至这个对象。

- 3.最后返回this指向的新对象。</b>

我们用代码模拟这三句话，就像这样：

```
var Person = function () {
  // var this = {};
  this.name = "echo";
  this.sayName = function () {
    console.log('my name is '+ this.name);
  };
  // return this; 这里隐性返回的其实就是上面创建的空对象，这个空对象被赋予了name属性和一个sayName方法
}
var me = new Person();
me.sayName();//my name is echo
```
 在这段代码中，sayName()方法被添加到了this中，但有个问题，不管我们执行几次new Person()，sayName()方法会反复的被添加到this中，且每次sayName()方法都会在内存中新开内存。

当我们所有实例中的sayName()方法都是一模一样时，这种做法是很浪费内存的，推荐做法是将sayName()方法添加在Person的原型中。

```
var Person = function () {
  this.name = "echo";
};
Person.prototype.sayName = function () {
  console.log('my name is '+ this.name);
};
var me = new Person();
me.sayName();//my name is echo
```
关于new一个构造函数到底发生了什么，我在前面说会隐性的新建一个空对象赋予this，还是那句话，这里的空对象并不是严格意义上的空，它还是继承了Person的原型，准确来说应该是这样。
```
// var this = Object.create(Person.prototype);
```
 ## 五、构造函数的返回值

概要：构造函数能返回什么？默认返回什么？

当我们new一个构造函数总是会返回一个对象，默认返回this所指向的对象。如果我们没有在构造函数内为this赋予任何属性，则会返回一个集成了构造函数原型，没有自己属性的'空对象'。(如果读不懂这句话，请结合new发生的过程去理解)

尽管我们没在构造函数内写return语句，也会隐性的返回this，但其实我们可以返回自定义的对象。像这样：

```
var Person = function () {
  this.name = "echo";
  var that = {};
  that.name = "wl";
  return that;
};
var me = new Person();
me.name;//wl
```
构造函数可以返回任意对象，只要你返回的是个对象。假设你返回的不是对象，程序也不会报错，但这个返回值会被忽略，最终还是隐性的返回this所指向的对象。

```
var Person = function () {
  this.name = "echo";
  var name = "wl";
  return name;
};
var me = new Person();
me.name; //echo
```
## 六、强制使用new 的模式

概要：不使用new调用构造函数会怎样？构造函数内能自定义对象吗？不使用new也能继承构造函数原型的做法

构造函数与普通函数无异，只是调用需要使用new，当我们不使用new调用时，语法也不会出错，但函数中的this会指向全局对象(非严格模式是window)。
```
var Person = function () {
  this.name = "echo";
};
var me = Person();
window.name//echo
```
在这段代码中实际上创建了一个全局对象属性name，你可以通过window.name访问到它。那么说到这里对于构造函数我们强调两点。

1.构造函数名首字母大写

2.调用构造函数使用new

遵守这些约定肯定是好的，但在实际开发的构造函数中，我们常常看看使用that等其它字面量代替this的做法。这么做的目的是为了确保构造函数按照自己定义的方式执行，而不存创建空对象赋予this等隐性不可见的行为，更可预测。

```
var Person = function () {
  var that = {};
  that.name = "echo";
  return that;
};
var me = new Person();
me.name;//echo
```
对于上述代码中，我们使用that代替了this，使用that只是一种命名约定，你可以使用self，me甚至任意非js语言保留字的字段。

或者that都不创建，直接返回一个对象。

```
var Person = function () {
  return {
    name : "echo"
  };
};
var me = new Person();
var you = Person();
me.name//echo
you.name//echo
```
这种写法不管我们是否使用new去调用，都能得到一个实例，但这种模式丢失了原型，所有的实例都不会继承Person()原型上的属性。

```
var Person = function () {
  return {
    name:'echo'
  }
};
Person.prototype.sayName = function () {
  console.log(1);
};

me.sayName();//报错，自定义对象未指向Person，没继承Person的方法
you.sayName();//报错，同上
```
我们在前面说，不使用new时，this指向window(非严格模式)，无法继承Person的任何属性。

```
var Person = function () {
  this.name = "echo"
};
Person.prototype.sayName = function () {
  console.log(1);
};
var me = new Person();
var you = Person();
me.name;//echo
you.name;//报错，此时的name是window的属性
me.sayName();//1
you.sayName();//报错，在实例化过程中this指向window，并未继承Person的方法
```
那有办法可以让不使用new情况下实例也能继承Person属性的做法吗，当然有，比如调用自身的构造函数：

```
var Person = function () {
  if(!(this instanceof Person)){
    return new Person();
  }
  this.name = "echo"
};
Person.prototype.sayName = function () {
  console.log(1);
};
var me = new Person();
var you = Person();
me.name;//echo
you.name;//echo
me.sayName();//1
you.sayName();//1
```
看到没，没使用new的实例you也继承了Person的属性和方法，如果看不懂那应该是对于instanceof运算符不太了解，这里顺带说下

instanceof运算符用于测试构造函数的prototype属性是否出现在对象的原型链中的任何位置---MDN

object instanceof constructor   object：要检测的对象.    constructor：某个构造函数

```
function Car(make, model, year) {
  this.make = make;
  this.model = model;
  this.year = year;
}
var auto = new Car('Honda', 'Accord', 1998);
console.log(auto instanceof Car);//true
console.log(auto instanceof Object);//true
```
那么上述代码中，new Person()好理解，this就是隐性返回的实例，this instanceof Person为true跳过判断，直接走new一个构造函数时发生的过程，得到实例自然会继承Person的属性和方法。

Person()呢，this指向window，很明显this instanceof Person为false，假假为真，执行判断内的代码new Person()；同理，也走了new过程的三部曲，得到的实例也继承了Person的属性和方法。