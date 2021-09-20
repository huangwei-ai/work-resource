## 深入理解css中position属性及z-index属性
　　在网页设计中，position属性的使用是非常重要的。有时如果不能认识清楚这个属性，将会给我们带来很多意想不到的困难。
position属性共有四种不同的定位方法，分别是`static、fixed、relative、absolute,sticky`。最后将会介绍和position属性密切相关的z-index属性。

## 第一部分：position: static
　　static定位是HTML元素的默认值，即没有定位，元素出现在正常的流中，因此，这种定位就不会收到top，bottom,left,right的影响。
如html代码如下：
```
<div class="wrap">
    <div class="content"></div>
</div>
```
css代码如下：
```
.wrap{width: 300px;height: 300px; background: red;}
.content{position: static; top:100px; width: 100px;height: 100px; background: blue;}
```
效果图如下：
![image text](https://images2015.cnblogs.com/blog/1044137/201611/1044137-20161128205915615-1507273054.png)

我们发现，虽然设置了static以及top，但是元素仍然出现在正常的流中。

## 第二部分：fixed定位
　　fixed定位是指**元素的位置相对于浏览器窗口是固定位置**，即使窗口是滚动的它也不会滚动，且fixed定位**使元素的位置与文档流无关**，因此不占据空间，且它会和其他元素发生重叠。
html代码如下：
```
<div class="content">我是使用fix来定位的！！！所以我相对于浏览器窗口，一直不动。</div>
```
css代码如下：
```
body{height:1500px; background: green; font-size: 30px; color:white;}
.content{ position: fixed; right:0;bottom: 0; width: 300px;height: 300px; background: blue;}
```
效果图如下：
![image text](https://images2015.cnblogs.com/blog/1044137/201611/1044137-20161128211445818-1221108436.png)
即右下角的div永远不会动，就像经常弹出来的广告！！！
值得注意的是：fixed定位在IE7和IE8下需要描述！DOCTYPE才能支持。

## 第三部分：relative定位
　　相对定位元素的定位是**相对它自己的正常位置的定位**。

　　关键：如何理解其自身的坐标呢？

　　让我们看这样一个例子，hmtl如下：
```
<h2>这是位于正常位置的标题</h2>
<h2 class="pos_bottom">这个标题相对于其正常位置向下移动</h2>
<h2 class="pos_right">这个标题相对于其正常位置向右移动</h2>
```
　　css代码如下：
```
.pos_bottom{position:relative; bottom:-20px;}
.pos_right{position:relative;left:50px;}
```
　　效果图如下：
![image text](https://images2015.cnblogs.com/blog/1044137/201611/1044137-20161128212852302-1560083511.png)
即bottom:-20px;；向下移动。  left:50px;向右移动。

即可以理解为：**移动后是移动前的负的位置**。

比如上例中，移动后是移动前负,bottom:-20px;即移动后是移动前bottom:20px;也就是说，移动后是移动前的向下20px；

又如：left:50px;移动后是移动前左边的-50px;那么也就是说移动后是移动前的右边的50px。

即：**移动后对于移动前：如果值为负数，则直接换成整数；如果值为整数，则直接改变相对方向**。

弄清楚了relative是如何移动的，下面我们看一看移动之后是否会产生其他的影响。

html代码如下：
```
<h2>这是一个没有定位的标题</h2>
<h2 class="pos_top">这个标题是根据其正常位置向上移动</h2>
<p><b>注意:</b> 即使相对定位元素的内容是移动,预留空间的元素仍保存在正常流动。</p>
```
css代码如下：
```
h2.pos_top{position:relative;top:-35px;}
```
效果图如下：
![image text](https://images2015.cnblogs.com/blog/1044137/201611/1044137-20161128214422943-220742666.png)

根据之前的说法，top:-35px；值是负数，则直接换成正数，即移动后相对与移动前向上偏移了35px；我们发现于上，移动后和上面的元素发生了重叠；于下，**即使相对元素的内容移动了，但是预留空间的元素仍然保存在正常流动，也就是说相对移动之后，不会对下面的其他元素造成影响**。

>注意：top：20px；是指子元素的margin外侧和包裹元素的border内侧之间的距离是20px。

## 第四部分：absolute定位
>绝对定位的元素相对于最近的已定位父元素，如果元素没有已定位的父元素，那么它的位置相对于`<html>`。
下面举几个例子：
例子1：
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>绝对定位</title>
    <style>                
    body{background:green;
        }
    .parent{ 
        width: 500px;
        height: 500px;
        background: #ccc;
        }
    .son{ 
        width: 300px;
        height: 300px;
        background: #aaa;
        }
    span{
        position: absolute; 
        right: 30px; 
        background: #888;
        }
    </style>
</head>
<body>
    <div class="parent">
        <div class="son">
            <span>什么？</span>
        </div>
    </div>
</body>
</html>
```
　效果如下：
![image text](https://images2015.cnblogs.com/blog/1044137/201611/1044137-20161128223024646-1005050992.png)

即我只在span中设置了position:absolute；而在其父元素中都没有，于是它的位置是相对于html的。

例2:
```
.son{position: relative; width: 100px;height: 100px;background: #aaa; }
```
　相较于上一个例子，我只修改了class为son的元素的css，设置为`position：relative`；效果图如下：
![image text](https://images2015.cnblogs.com/blog/1044137/201611/1044137-20161128223443568-1391473789.png)

于是，我们发现现在span的位置是相对于设有position属性的class为son的父元素的。

例3：
```
.parent{position: absolute; width: 300px;height: 300px;background: #ccc;}
```
　这个例子我只是修改了第一个例子中的css--设置了position:absolute；效果如下：
![image text](https://images2015.cnblogs.com/blog/1044137/201611/1044137-20161128223803959-116436131.png)

于是我们发现，现在span的定位是相对于具有position:absolute的属性的class为parent的父元素。

例4：
```
.parent{position:fixed; width: 300px;height: 300px;background: #ccc;}
```
相对于例1，我添加了fixed的position属性，发现结果和例3是一模一样的。

例5：
```
.parent{position:static; width: 300px;height: 300px;background: #ccc;}
```
相对于例1，我添加了static的position属性（即html的默认属性），结果和例1是一样的。

>综上所述，当某个absolute定位元素的父元素具有position:relative/absolute/fixed时，定位元素都会依据父元素而定位，而父元素没有设置position属性或者设置了默认属性，那么定位属性会依据html元素来定位。

## 第五部分：重叠的元素--z-index属性
>首先声明：z-index只能在position属性值为relative或absolute或fixed的元素上有效。

>基本原理是：z-index的值可以控制定位元素在垂直于显示屏幕方向（z轴）上的堆叠顺序(stack order),值大的元素发生重叠时会在值小的元素上面。
- index设置为相等时，和没有相同是一样的
- 普通情况父元素与子元素的z-index不能做比较
- 当子元素的z-index是负数的时候可以和父元素相比较

## 第六部分：脱离文档流导致的问题
　　我们知道如果使用`position:absolute`和`position:fixed`都会导致元素脱离文档流，由此就会产生相应的问题。举例如下：
```
<!DOCTYPE html>
<html>
<head>
    <title>position</title>
    <style>
        .div1{
            background-color: red;
            padding:20px;
        }
        .div2{
            width: 200px;
            height: 200px;
            background-color: blue;
        }
    </style>
</head>
<body>
    <div class="div1">
        <div class="div2"></div>
    </div>
</body>
</html>
```
这时效果如下：
![image text](https://images2015.cnblogs.com/blog/1044137/201702/1044137-20170212121234041-834180066.png)

即子元素将父元素撑了起来。

**但是一旦子元素的position为fixed或者是absolute，那么它就会脱离文档流，这样的后果是父元素无法被撑开**，如下所示：

```
<!DOCTYPE html>
<html>
<head>
    <title>position</title>
    <style>
        .div1{
            background-color: red;
            padding:20px;
            position: relative;
        }
        .div2{
            position: absolute; // 添加position:absolute使其脱离文档流
            width: 200px;
            height: 200px;
            background-color: blue;
        }
    </style>
</head>
<body>
    <div class="div1">
        <div class="div2"></div>
    </div>
</body>
</html>
```
　　最终效果如下所示：
![image text](https://images2015.cnblogs.com/blog/1044137/201702/1044137-20170212121447838-787577860.png)

- 解决方法1：在js中设置父元素高度等于子元素的高度。
- 解决方法2：给父元素强行设置高度。（对于宽度导致的类似问题就强行设置宽度）

 
## 第七部分：position: sticky;
　　在一个内容中，我们可以固定一个部分，然后到了另一个内容，又会固定另外一个部分。
　position属性中最有意思的就是sticky了，设置了sticky的元素，**在屏幕范围（viewport）时该元素的位置并不受到定位影响（设置是top、left等属性无效），当该元素的位置将要移出偏移范围时，定位又会变成fixed，根据设置的left、top等属性成固定位置的效果**。

可以知道sticky属性有以下几个特点：

- 该元素并不脱离文档流，仍然保留元素原本在文档流中的位置。
- 当元素在容器中被滚动超过指定的偏移值时，元素在容器内固定在指定位置。亦即如果你设置了top: 50px，那么在sticky元素到达距离相对定位的元素顶部50px的位置时固定，不再向上移动。
- 元素固定的相对偏移是相对于离它最近的具有滚动框的祖先元素，如果祖先元素都不可以滚动，那么是相对于viewport来计算元素的偏移量

比较蛋疼的是这个属性的兼容性还不是很好，目前仍是一个试验性的属性，并不是W3C推荐的标准。它之所以会出现，也是因为监听scroll事件来实现粘性布局使浏览器进入慢滚动的模式，这与浏览器想要通过硬件加速来提升滚动的体验是相悖的。

简单的说，要让sticky属性生效的条件有以下两点：

- 一个是元素自身在文档流中的位置
- 另一个是该元素的父容器的边缘

第一点上面已经讲过了，如果设置了top: 50px，那么元素在达到距离顶部50px时才会发生定位，否则并不会发生定位。

第二点则需要考虑父容器的高度情况：sticky元素在到达父容器的底部时，则不会再发生定位，如果父容器高度并没有比sticky元素高，那么sticky元素一开始就达到了底部，并不会有定位的效果。

此外还有一点就是父元素的overflow属性，如果父元素的overflow属性并不是默认的visible属性，那么sticky元素则相对于该父元素定位。也就是如果要定位在顶部的话，此时这个效果就无效了。。。

具体情况可以看下图，基本上可以说这个属性使用的浏览器只有FireFox和iOS的Safari：

同样也可以设置top值， 这个值是border上边缘和包裹元素的下边缘之间的距离，但是一旦滚动起来，就是和浏览器顶部的距离了，话不多说，直接上demo，一看便懂。
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>alink</title>
    <style>
        body {
            margin: 0;
            padding: 0;
        }
        .wrap {
            border: 20px solid blue;
        }
        .header {
            position: sticky;
            top: 20px;
            border: 20px solid red;
            margin-top: 20px;
        }
    </style>
</head>
<body>
    <div class="wrap">
        <div class="header">
            这是头部
        </div>
        <div class="content">
            这是内容部分<br>
           
        </div>
    </div>
    <div class="wrap">
        <div class="header">
            这是另一个头部
        </div>
        <div class="content">
            这是另一个内容<br> 
        </div>
    </div>
</body>
</html>
```