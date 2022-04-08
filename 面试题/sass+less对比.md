## 如何实现两个特别大的数相加
- 但是 JS 在存放整数的时候是有一个安全范围的，一旦数字超过这个范围便会损失精度。
- 我们不能拿精度损失的数字进行运行，因为运算结果一样是会损失精度的。
- 所以，我们要用字符串来表示数据！（不会丢失精度）
- JS 中整数的最大安全范围可以查到是：9007199254740991
```
let a = "9007199254740991";
let b = "1234567899999999999";

function add(a ,b){
   //取两个数字的最大长度
   let maxLength = Math.max(a.length, b.length);
   //用0去补齐长度
   a = a.padStart(maxLength , 0);//"0009007199254740991"
   b = b.padStart(maxLength , 0);//"1234567899999999999"
   //定义加法过程中需要用到的变量
   let t = 0;
   let f = 0;   //"进位"
   let sum = "";
   for(let i=maxLength-1 ; i>=0 ; i--){
      t = parseInt(a[i]) + parseInt(b[i]) + f;
      f = Math.floor(t/10);
      sum = t%10 + sum;
   }
   if(f == 1){
      sum = "1" + sum;
   }
   return sum;
}
```

## 什么是SSR
- SSR是Server Side Render简称，叫服务端渲染
> 在客户端请求服务器的时候，服务器到数据库中获取到相关的数据，并且在服务器内部将Vue组件渲染成HTML，并且将数据、HTML一并返回给客户端，这个在服务器将数据和组件转化为HTML的过程，叫做服务端渲染SSR

而当客户端拿到服务器渲染的HTML和数据之后，由于数据已经有了，客户端不需要再一次请求数据，而只需要将数据同步到组件或者Vuex内部即可。除了数据以外，HTML也结构已经有了，客户端在渲染组件的时候，也只需要将HTML的DOM节点映射到Virtual DOM即可，不需要重新创建DOM节点，这个将数据和HTML同步的过程，又叫做客户端激活

- 使用SSR的好处
有利于SEO
其实就是有利于爬虫来爬你的页面，因为部分页面爬虫是不支持执行JavaScript的，这种不支持执行JavaScript的爬虫抓取到的非SSR的页面会是一个空的HTML页面，而有了SSR以后，这些爬虫就可以获取到完整的HTML结构的数据，进而收录到搜索引擎中。

白屏时间更短
相对于客户端渲染，服务端渲染在浏览器请求URL之后已经得到了一个带有数据的HTML文本，浏览器只需要解析HTML，直接构建DOM树就可以。而客户端渲染，需要先得到一个空的HTML页面，这个时候页面已经进入白屏，之后还需要经过加载并执行JavaScript、请求后端服务器获取数据、JavaScript 渲染页面几个过程才可以看到最后的页面。特别是在复杂应用中，由于需要加载 JavaScript 脚本，越是复杂的应用，需要加载的 JavaScript 脚本就越多、越大，这会导致应用的首屏加载时间非常长，进而降低了体验感。

## node内存泄漏
- 循环引用：你引用我，我引用你
- 无节制循环
- 过大数组循环
- 闭包

## less和sass的使用
【Less中的变量】
　　1、声明变量：@变量名:变量值;
　　　  使用变量：@变量名
```
@length:100px;
@color:yellow;
@opa:0.5;
```
　　>>>Less中变量的类型：
　　　　①数字类：1 10px
　　　　②字符串：无引号字符串 red 有引号字符串 "haha"
　　　　③颜色类：red #000000 rgb()
　　　　④值列表类：用逗号或空格分隔10px solid red
　　>>>变量使用原则：多次频繁出现的值、需要修改的值，设为变量。
```
#div1{
    width: @length;
    height: @length;
    background-color: @color;
    opacity: @opa;
    .borderRadius;
    .setMargin(top_,10px);
}
```

2、混合(Mixins)
　　①无参混合
　　　声明：.name{}; 选择器中调用 .name
　　②带参混合
　　　无默认值声明：.name(@param){} 调用：.name(parValue);
　　　有默认值声明：.name(@param:value){} 调用：.name(parValue); parValue可省
　　　　>>>如果声明时，参数没有默认值，则调用时必须赋值，否则报错
　　　　>>>无参混合，会在css中编译出同名的class选择器；有参的不会
3、Less的匹配模式
　　使用混合进行匹配，类似于if结构；
　　声明：.name(条件一,参数){} .name(条件二,参数){} .name(条件三,参数){} .name(@_,参数){}
```
.setMargin(top_,@width){
    margin-top: @width;
}
.setMargin(bottom_,@width){
    margin-bottom: @width;
}
.setMargin(@_,@width){
    padding: 10px;
}
```
　　调用：.name(条件值,参数值);
```
.setMargin(top_,10px);
```
　　匹配规则：根据调用时提供的条件值，去寻找与之匹配的"Mixins"执行。其中@_表示永远需要执行的部分。
4、Less中的运算
　　+ - * /
　　颜色计算时，红绿蓝分三组计算。组内可进位，组间互不干涉

【Less中的嵌套】
　　1、嵌套默认是后代选择器，如果需要子代选择器，则在子代前面加>
　　2、& 表示上一层，&:hover 表示上一层的hover事件
```
section{
    p{
        color: blueviolet;
        background-color: #00FFFF;
        text-align: center;
    }
    ul{
        padding: 0px;
        list-style: none;
        li{
            float: left;
            margin: 10px;
            width: 100px;
            border: 3px solid #00CED1;
            text-align:center;
            &:hover{
                background-color: cornflowerblue;
                color: white;
            }
        };
    }
}
```
【Sass中的变量】
　　1、声明变量：$变量名：变量值
　　2、变量在字符串中嵌套，需使用#{}包裹
```
border: #{$i}px solid red;　
```
　　3、Sass中的运算：会将单位也进行运算。使用时需注意最终单位
　　　　10px*10px=100px*px
　　4、混合宏、继承、占位符
　　　　①混合宏：声明@mixin name($param:value){}

```@mixin hong($color:yellow){
    color: $color;
}```
　
　　　　　　　　
调用@include name(value);


```@include hong;```
　
　　　　>>>声明时可以有参，也可无参；可带默认值，也可不带；但是调用时必须符合声明规范，同less
　　　　>>>优点：可以传参
　　　　>>>缺点：会将混合宏中代码copy到对应的选择器中，产生冗余代码
　　　　②继承：声明：.class{}

```.class{
    padding: 10px;
}
```
调用：@extend.class;
```
@extend .class;
```
　　　　>>>优点：继承的相同代码，可以提取到并集选择器；不会生成同名的class选择器

　　　　>>>缺点：无法进行传参
　　综上所述：当需要传递参数时，用混合宏；当有现成class时用继承；当不需要class时，用占位符
　　5、if条件结构
　　　　@if 判断条件{}
　　　　@else{}
　　6、for循环结构
　　　　@for $i from 1 to 10{} 不含10
　　　　@for $i from 1 through 10{} 包含10
```
@for $i from 1 through 4{
    .border#{$i}{
        border: #{$i}px solid red;
    }
}
```
　　7、while循环结构
　　　　$j:1,
　　　　@while 判断条件{}
```
$j:1;
@while $j<4{
    .while#{$j}{
        border:#{$j}px solid red;
    }
    $j:$j+1;
}
```
　　8、each循环遍历
```
　　　　@each $item in a,b,c,d{}
```
　　9、函数
```
　　　　@function func($name){}
```
```
@function func($length){
    $length:$length*5;
    @return $length;
} 
```
　　【Sass中的嵌套】

　　1、选择器嵌套 ul{ li{} } 后代
　　　　ul{ >li{} } 子代
　　　　&表示上一层 div{ ul {li{ &=="div ul li" } } }
　　2、属性嵌套：属性名与{之间必须有: 例如 border:{color:red;}
　　3、伪类嵌套:ul{li{ &:hover{ "ul li:hover " } } }
```
section{
    p{
        color: #2173B6;
        background-color: #00FFFF;
    }
    ul{
        padding: 0px;
        list-style: none;
        li{
            float: left;
            width: 100px;
            text-align: center;
            margin: 10px;
            border: {
                color: #4E81BD;
                style:solid;
                width: 10px;
            };
            &:hover{
                background-color: #6495ED;
                color: white;
            }
        }
    }
}
```

```

##  less和sass优缺点对比
### 为什么要使用CSS预处理器？

CSS有具体以下几个缺点：
- 语法不够强大，比如无法嵌套书写，导致模块化开发中需要书写很多重复的选择器；
- 没有变量和合理的样式复用机制，使得逻辑上相关的属性值必须以字面量的形式重复输出，导致难以维护。
- 这就导致了我们在工作中无端增加了许多工作量。而使用CSS预处理器，提供 CSS 缺失的样式层复用机制、减少冗余代码，提高样式代码的可维护性。大大提高了我们的开发效率。
- 但是，CSS预处理器也不是万金油，CSS的好处在于简便、随时随地被使用和调试。预编译CSS步骤的加入，让我们开发工作流中多了一个环节，调试也变得更麻烦了。更大的问题在于，预编译很容易造成后代选择器的滥用。
- 所以我们在实际项目中衡量预编译方案时，还是得想想，比起带来的额外维护开销，CSS预处理器有没有解决更大的麻烦。

### Sass和Less的比较

相同之处：
- 1、混入(Mixins)——class中的class；
- 2、参数混入——可以传递参数的class，就像函数一样；
- 3、嵌套规则——Class中嵌套class，从而减少重复的代码；
- 4、运算——CSS中用上数学；
- 5、颜色功能——可以编辑颜色；
- 6、名字空间(namespace)——分组样式，从而可以被调用；
- 7、作用域——局部修改样式；
- 8、JavaScript 赋值——在CSS中使用JavaScript表达式赋值。

不同之处：
- 1、Less环境较Sass简单
sass的安装需要安装Ruby环境，Less基于JavaScript，是需要引入Less.js来处理代码输出css到浏览器，也可以在开发环节使用Less，然后编译成css文件，直接放在项目中，有less.app、SimpleLess、CodeKit.app这样的工具，也有在线编辑地址。

- 2、Less使用较Sass简单
LESS并没有裁剪CSS原有的特性，而是在现有CSS语法的基础上，为CSS加入程序式语言的特性。只要你了解CSS基础就可以很容易上手。

- 3、从功能出发，Sass较Less略强大一些
①sass有变量和作用域。
```
-$variable，likephp；

-#｛$variable｝likeruby；
```
- 变量有全局和局部之分，并且有优先级。

②sass有函数的概念；
- @function和@return以及函数参数（还有不定参）可以让你像js开发那样封装你想要的逻辑。
- @mixin类似function但缺少像function的编程逻辑，更多的是提高css代码段的复用性和模块化，这个用的人也是最多的。
- ruby提供了非常丰富的内置原生api。

③进程控制：
- 条件：@if@else；
- 循环遍历：@for@each@while
- 继承：@extend
- 引用：@import

④数据结构：
- $list类型=数组；
- $map类型=object；
其余的也有string、number、function等类型

4、Less与Sass处理机制不一样

- 前者是通过客户端处理的，后者是通过服务端处理，相比较之下前者解析会比后者慢一点

5、关于变量在Less和Sass中的唯一区别就是Less用@，Sass用$。
