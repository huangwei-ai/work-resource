## flex——内容宽度等分
一句口诀记住，父元素 display:flex，子元素 flex:1
```
//css
       .box {
            display: flex;
        }
        
        .box div {
            flex: 1;//内容自动放大，占满空间
            border: 1px solid red;
        }
//html
    <div class="box">
        <div>1</div>
        <div>2</div>
        <div>3</div>
    </div>
```
![image text](https://upload-images.jianshu.io/upload_images/15081804-fad92597fd3fe0ca.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

## 左右布局，一侧定宽，一侧自适应撑满
```
//css
        html,
        body {
            height: 100%
        }
        
        .main {
            display: flex;
            height: 100%;
        }
        
        .left,
        .right {
            height: 100%;
            border: 1px solid red;
            box-sizing: border-box;
        }
        
        .left {
            width: 300px;
            flex: none;
        }
        
        .right {
            width: 100%;
        }

//html
    <div class="main">
        <div class="left">固定宽度300px</div>
        <div class="right">自适应宽度</div>
    </div>
```
![image text](https://upload-images.jianshu.io/upload_images/15081804-83a10fc0764b1fe4.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

## 未知高度，上下左右居中
```
//css
        html,
        body {
            height: 100%
        }
        
        .main {
            display: flex;
            height: 100%;
            justify-content: center;
            align-items: center
        }
        
        .box {
            width: 300px;
            border: 1px solid red;
        }
    
    //html
     <div class="main">
        <div class="box">未知高度上下左右居中</div>
    </div>
```

## 左右定宽，中间自适应
- flex:flex 简单的做到左右定宽，中间自适应。一个口诀就可以搞定，中间 flex = 1, 宽用百分比，左右固定宽，父元素 display:flex。
- float:中间加 margin，左右加 float，先加左右，后加中间。
中间：
```
 width:100%;
 height: 100px;
 margin-left: 100px;
 margin-right: 100px;
```
左：
```
float: left; 
width:100px;
```
右：
```
float: right;
 width:100px;
```
## 三个小盒子在一个大盒子居中排列
```
 <div class="box">
        <span class="left"></span>
        <span class="mid"></span>
        <span class="right"></span>
    </div>
```
```
 .box {
            height: 300px;
            width: 300px;
            background: red;
            display: flex;//水平居中
            align-items: center;
            flex-direction: column;//垂直居中
            //水平垂直居中
            align-items: center;
            justify-content: center;
        }
```

