>loader：是webpack用来预处理模块的，在一个模块被引入之前，会预先使用loader处理模块的内容

可能，你会遇到当你用webpack打包的时候，提示你需要一个loader来处理文件，那webpack中的loader就是帮助预处理下模块中的内容

默认webpack只会处理js代码，所以当我们想要去打包其他内容时，让webpack处理其他类型的内容，就要使用相应的loader

## 使用方法：

- 1、在配置文件中（我的是webpack.config.js）加入module属性，该属性是一个对象,在这个属性中有一个rules字段，嗯，先看一下例子，然后再解释
```
module:{
        rules:[{
            test:/\.js$/,
            use:[{
                loader:'babel-loader',
                options:{
                    presets:['react']
                }
            }]
        }]
    }
```
我们可以看到rules属性的属性值为一个数组，每一个数组成员都是一个对象，可以配置不同的规则

- test:test后是一个正则表达式，匹配不同的文件类型

- use:在这个规则中，当你匹配了这个文件后，使用什么样的loader去处理匹配到的文件，use接收的是一个数组，意味着当他匹配到文件后，它可以启用很多的loader去处理文件的内容

use中可以有字符串和对象，当我们需要对loader进行额外的配置时，需要用到对象，如果我们使用的是loader默认的配置，就直接字符串（对应的loader）即可

上面的例子中，因为我们需要对设置预设，所以就将其放在了对象中，该对象中loader：后面是需要的loader

options:{}对应的loader进行的一些配置

当然，还会有其他的一些属性比如（exclude、include等），这里就不再详细赘述

- 2、使用这些loader前，我们还需要先下载好和这个loader相关的一些包，所以在你的命令行中使用  npm i -D  +要安装的模块名（执行完之后，在package.json中就多了对应的包）

- 3、然后就是运行了，当然这只是loader使用时需要准备的大致东西，但每个loader的使用还要具体来说，下面我们举几个常用的例子，把步骤写一下

## 一、webpack处理jsx内容

- 1、因为需要用到react，所以先安装react和react-dom库，所以执行下面的命令（注意如果还没有安装依赖，要先执行npm i，这条命令，会自动到package.json中查看devDependencies和dependencies中声明了哪些包，然后会先把这些包安装好）

```npm i -S react react-dom```
- 2、在入口文件（我这里使用index.js）中引入上面的两个库
```
import React  from 'react';
import ReactDOM  from 'react-dom';
```
然后就可以使用相应的jsx语句了
```
ReactDOM.render(
    <div className="fa fa-rocket"><div className={style.ot}>React</div></div>,
 
    document.getElementById('root')/*上面的逗号不可少，此处不可加分号*/
);
```
- 3、因为是jsx文件，所以我们需要使用相应的loader来对其jsx内容进行处理，在使用loader之前，应该先安装好和这个loader相关的一些包,如下
```
npm i -D babel-loader  babel-core
```
- 4、使用上面的两个包，在配置文件中加入module属性
```
module:{
        rules:[{
            test:/\.js$/,
            use:[{
                loader:'babel-loader',
                options:{
                    presets:['react']
                }
            }]
        }]
    }
```
使用babel-loader，其实本质上就是使用babel这个编译器去编译一些文件，我们需要的是js文件，它可以编译**葛洪语法**，把**ES6编译为ES5**，把**jsx编译为js代码**，所以再我们没有告诉babel-loader使用什么样的东西去编译什么样的语法之前，它是不会做任何工作的，所以我们现在要处理的是jsx的代码，需要一个预设来告诉babel-loader，当你匹配到jsx文件之后，你要启用这个预设去处理相应的内容，我们要使用的预设叫做react，也就是上面的
presets:['react']

- 5、可以去打包了，npm run dev（dev是自己起的名字，打包用的）

>分析总结：我们在入口文件中使用了jsx的语法，这些代码，不是真正的js代码，所以，不能直接去运行这个js文件，如果浏览器加载了这个js文件，发现里面有一些不是js代码，它就会报错

此时，我们要将里面的代码进行一些转换，转换的关键就在于你使用什么样的loader去处理里面的内容，所以我们在配置里面先匹配到一种叫js的文件，只要你是js的文件被匹配到，在打包的过程中，webpack发现了你这个文件是js文件，webpack就会启用相应的设置去处理这个被匹配到的文件里面的内容，我们在这里声明了一个babel-loader,当js文件被找到时，它就会先用babel-loader去处理一遍这个入口文件里面的内容，然后，我们还对这个loader进行了一些设置，所以在打包的过程中，它会先把jsx代码转换成了js代码，然后就顺利地输出了js打包后的文件，此时，代码就可以正常运行了

## 二、引入样式文件

- 1、使用css文件时，只需要在入口文件引入你的样式文件
```
import './style/main.css';
```
- 2、安装需要用到的loader
```
npm i -D css-loader
npm i -D style-loader
```
css-loader:当匹配到css文件时，就要用css-loader对css样式进行处理

style-loader:当有样式被打包到我们的入口文件时，style-loader会把打包的样式插入到我们的HTML结构中

- 3、在配置文件中进行相应的配置，在module，中rules中加入下面的规则
```
{
                test:/\.css$/,
                use:['style-loader','css-loader'],
            }
```
use中的style-loader和css-loader顺序不能变，因为loader的处理有一个优先级，从右到左、从下到上

很显然，只有当我们对css文件进行处理打包之后才将其插入到HTML结构中，所以要css-loader在右侧，style-loader在左侧

- 4、npm run dev（或者可以直接run start）

## 三、引入图片

### 1>、背景图片

css-loader会处理css的资源，当它遇到一个url的时候，css-loader会帮助我们处理url里面的东西，当出现url，css-loader就会知道，可能要加载一些东西，要引入一些东西，这时，css-loader会帮助你去引入这个模块，之后webpack会帮助你处理这个模块

css-loader会处理url里面的内容，帮助引入里面的资源，在webpack里面，引入资源，这些资源也会被称之为模块，像图片这些模块，webpack根本搞不定，所以需要一个loader来处理这种类型的模块

所以专门处理图片的loader是file-loader

- 1、首先，因为背景图片也是在css中引入的，所以需要引入css相关的loader是一定，上面已经有了，这里就不再重复写了

- 2、安装对应的loader包
```
npm i -D file-loader
```
- 3、在配置文件中进行相应的配置，道理和上面的差不多，直接上代码
```
{
                test:/\.jpg|png|jpeg|gif$/,
                use:['file-loader']
            }
```
file-loader主要做了两件事：
- 1、把url里面内容转换成我们最终需要使用的那个路径。
- 2、把图片转移到我们输出的目录，并且把图片更改为另外一种名字或者做一些其他的处理

## 2>、在HTML中插入的图片

都是图片，所以用到的自然也是file-loader这个loader，具体的配置，看上面的，主要说说不同之处

首先我们需要在入口文件中导入图片
```
import dog  from './common/imgs/dog.jpg'
ReactDOM.render(
    <div><img src={dog}/></div>,
document.getElementById('root')
 
 );
```
当我们在模块里面使用import来引入一个图片资源的时候，file-loader也会把这个图片移动到你的输出目录，给它更改一个名字，然后返回一个最终要加载的图片的一个路径

在css的背景图片和HTML结构中插入的图片，两者的不同是：**在HTML中插入图片需要自己手动引入一个图片资源，然后webpack会帮助你引入这个模块（资源），引入这个资源的时候，file-loader会帮助你做相应的处理，而在css文件中，直接是正常的写法即可，不需要自己去手动import一个文件资源，因为css-loader在遇到url时，会帮助你处理url中的内容，它在处理时，就会帮你引入了这个图片资源，然后用file-loader来处理这个图片资源**

### 3>、使用url-loader引入图片，可以说它是file-loader的增强版

url-loader会把我们的图片使用base64的形式编码成另外一种字符串，网页是可以识别这种编码的东西的，这样的好处是，它减少了图片的请求，你只要请求回了这个页面，图片也就过来了，可以减少网络的请求，但是如果图片过大，这个字符串就会变得特变大，让加载的文件变得特别大

所以如果图片很小，没必要让其重新请求图片，直接将其写进页面中，让浏览器去解析，当图片过大时，就不让他编码，看下面的实现过程
```
{
                test:/\.jpg|gif|png$/,
                use:[{
                    loader:'url-loader',
                    options:{
                        limit:10000//以bit为单位，当小于10000bit时，编码，大于10000bit时，不编码
                    }
                }]，
            }
```
>总结：当使用url-loader去处理一些资源的时候，默认会把所有的资源都是用base64的形式进行编码，但是我们可以给它一个limit属性去约束他，当资源小于某个值的时候，才去编码，当不小于这个值时，它其实是会把这个资源交个file-loader去处理
————————————————
参考文章
CSDN博主「冰雪为融」的原创文章
原文链接：https://blog.csdn.net/lhjuejiang/article/details/80230419