## TypeScript Interface(接口)
>类型检查专注于解析值所具有的"形态"，这是TypeScript的核心原则之一。这个有时候被称为"duck typing"或者"structural subtyping"。在TypeScript中，Interface中写入这些类型的命名规范，并且也是一种强有力的方式来对你的代码或者项目的外部代码进行约束。

## 实现第一个接口

要看看interface怎么工作的最简单的方式就是我们来写一个例子：
```
function printLabel(labelledObj: {label: string}) {
  console.log(labelledObj.label);
}
var myObj = {size: 10, label: "Size 10 Object"};
printLabel(myObj);
```
类型检查器将会检查调用的"printLabel"。printLabel函数需要有一个对象作为参数，并且这个对象有"label"属性，该属性值类型是string。注意，我们的对象其实有比这个更多的属性，但编译器只检查当前匹配和需要的属性的类型。

我们再写一次上面的例子，只是这次我们使用interface来描述所需的"label"属性值是一个字符串：

```
interface LabelledValue {
  label: string;
}
function printLabel(labelledObj: LabelledValue) {
  console.log(labelledObj.label);
}
var myObj = {size: 10, label: "Size 10 Object"};
printLabel(myObj);
```
"LabelledValue"是一个名称，我们用它来描述前面那个例子中的需求。它依然体现出需要一个字符串类型的并且名为"label"的属性。注意，我们没有明确的说明在其他语言中也是在"printLabel"函数传入对象以实现此接口。这里，我们也仅仅是提下这个问题。如果我们传递给函数的对象符合要求，那么它就被允许了。

值的指出的是，类型检查不需求这些属性是来自挑选或者排序之后的，只需要interface所需的属性是存在的，并且类型也是相对应的即可。

## 属性可选

在interface中，并不是所有的属性都是必需的。有些可能是在某个条件下才需要，不是一直需要用到。当用户将一个对象传入到一个只具有一对属性的函数时(如创建一个"option bags"的方式)，这些可选属性是很方便的。

这里有个这种方式的例子：
```
interface SquareConfig {
  color?: string;
  width?: number;
}
function createSquare(config: SquareConfig): {color: string; area: number} {
  var newSquare = {color: "white", area: 100};
  if (config.color) {
    newSquare.color = config.color;
  }
  if (config.width) {
    newSquare.area = config.width * config.width;
  }
  return newSquare;
}
var mySquare = createSquare({color: "black"});
```
可选属性的interface和写普通的interface相似，只是每个可选属性用"?"符号标识，以作为申明这是可选的。

可选属性的有点是，你可以描述这些可能会用到的属性，同时还可以捕捉你明确了的那些不期望使用的属性。举例说明，我们为传入的"createSquare"输入了一个错误的属性名称，我们需要抛出一个错误信息来通知我们。
```
interface SquareConfig {
  color?: string;
  width?: number;
}
function createSquare(config: SquareConfig): {color: string; area: number} {
  var newSquare = {color: "white", area: 100};
  if (config.color) {
    newSquare.color = config.collor;  // 类型检测器将会捕捉到你传入了错误的名称
  }
  if (config.width) {
    newSquare.area = config.width * config.width;
  }
  return newSquare;
}
var mySquare = createSquare({color: "black"});
```
## 函数类型
interface能够做和javascript的对象一样的事：描述形态的范围。除了描述一个对象的属性外，interface还可以描述函数类型。

用interface描述一个函数的类型，我们需要给interface一个调用标识。这就像一个只有参数列表和返回值类型的函数声明。
```
interface SearchFunc {
  (source: string, subString: string): boolean;
}
```
一旦定义后，我们就能和其他interface一样使用它了。在这里，我们将展示如何创建一个函数类型的变量并且将其赋值为相同类型的的函数值。
```
var mySearch: SearchFunc;
mySearch = function(source: string, subString: string) {
  var result = source.search(subString);
  if (result == -1) {
    return false;
  }
  else {
    return true;
  }
}
```
在函数类型正确的通过类型检查中，参数的名称不是必须匹配的。例如，上面的例子我们可以这样写：
```
var mySearch: SearchFunc;
mySearch = function(src: string, sub: string) {
  var result = src.search(sub);
  if (result == -1) {
    return false;
  }
  else {
    return true;
  }
}
```
函数参数一次只检查一个，相互检查每个位置对应参数的类型。在这里，我们的函数表达式的返回类型被它返回的值所表明(这里的false和true)。假如函数表达式返回的是number或者string，那么类型检查器将会通过返回类型与"SearchFunc" interface的描述类型不符用来警告我们。

## 数组类型

与我们使用interface来描述函数类型一样，我们也可以用interface来描述数组类型。数组类型有一个"index"类型用来描述了数组索引的类型，以及一个返回值类型用来表示相对应索引的元素类型。
```
interface StringArray {
  [index: number]: string;
}
var myArray: StringArray;
myArray = ["Bob", "Fred"];
```
支持的两种索引类型：string和number。数组可以同时使用这两种索引类型。但也存在限制，数字索引的返回值类型必须是字符串索引返回值类型的子类型。
```
interface Something {   
    [index: string]: number;
    [index: number]: number;  //数字索引的返回值类型是number,它是字符串索引的返回值类型的子类型；若[index: string]:string,则数字索引的返回值类型也必须是string
}
```
虽然索引标识是个用来描述数组及"dictionary"模式的很好的方式，同时也迫使所有属性需要匹配他们的返回类型。在这个例子中，属性类型与索引类型不匹配，类型检查程序给出错误：
```
interface Dictionary {
  [index: string]: string;
  length: number;    // 错误, 'length'的类型不是索引类型的子类型
} 
```
## 类类型

实现接口

在C#和Java中，接口最基础的用法是用来强制让类去符合某种特定的规则，而在TypeScript中也可以这样做。
```
interface ClockInterface {
    currentTime: Date;
}
class Clock implements ClockInterface  {
    currentTime: Date;
    constructor(h: number, m: number) { }
}
```
你也可以在接口中描述方法，然后在类中实现这个方法，就像我们下面例子中的"setTime"：
```
interface ClockInterface {
    currentTime: Date;
    setTime(d: Date);
}
class Clock implements ClockInterface  {
    currentTime: Date;
    setTime(d: Date) {
        this.currentTime = d;
    }
    constructor(h: number, m: number) { }
}
```
接口描述了类的公有部分，而非公有和私有两部分。使用它们来检查一个类的实例的私有部分所含的特殊类型是行不通的。

## 类的静态部分和实例部分的差异

当处理类和接口的时候，你需要记住类有两种类型：静态的类型和实例的类型。你可能注意到了，如果你创建一个带有构造签名的接口并尝试创建一个用来实现这个接口的类的时候，会产生一个错误：

```
interface ClockInterface {
    new (hour: number, minute: number);
}
class Clock implements ClockInterface  {
    currentTime: Date;
    constructor(h: number, m: number) { }
}
// 错误信息： Class 'Clock' incorrectly implements interface 'ClockInterface'
```
这是因为当用一个类来实现一个接口时，只检查该类的实例部分。由于构造函数位于静态部分，它不包含在该检查中。

所以，我们需要直接来操作类的静态部分。在这个例子中，我们直接与该类进行操作：

```
interface ClockStatic {
    new (hour: number, minute: number);
}
class Clock  {
    currentTime: Date;
    constructor(h: number, m: number) { }
}
var cs: ClockStatic = Clock;
var newClock = new cs(7, 30);
```
## 接口扩展

和类一样，接口也可以彼此间进行扩展。这个处理的任务是将一个接口的成员复制到另一个接口中，这样就可以更自由地把接口分离成可复用的组件。

```
interface Shape {
    color: string;
}
interface Square extends Shape {
    sideLength: number;
}
var square = <Square>{};
square.color = "blue";
square.sideLength = 10;
```
一个接口可以扩展多个接口，创建出一个包含所有接口的组合接口。
```
interface Shape {
    color: string;
}
interface PenStroke {
    penWidth: number;
}
interface Square extends Shape, PenStroke {
    sideLength: number;
}
var square = <Square>{};
square.color = "blue";
square.sideLength = 10;
square.penWidth = 5.0;
```
## 混合类型

正如我们之前所说，接口可以描述JavaScript世界里的丰富类型。因为JavaScript的动态的和灵活特性，你可能会偶尔碰到需要一个由上述多种类型组成的对象。

下面就有这么个例子，一个对象用来当做函数和对象一起使用，并且具有附加属性。

```
interface Counter {
    (start: number): string;
    interval: number;
    reset(): void;
}
function counter():Counter{
    var self = <Counter>function(start:number){
　　　　 self.interval = start;
        console.log("Hello World " + start);
    };
    self.interval = 10;
    self.reset = function(){
        self.interval = 10;
    }
    return self;
}
var c = counter();//c.interval = 10
c(5); //c.interval = 5
c.interval = 15; //c.interval = 15
c.reset(); //c.interval = 10
```
与JavaScript的第三方库交互时，您可能需要使用上述模式来全面的定义类型。