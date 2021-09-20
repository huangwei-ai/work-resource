HTML可以将元素分类方式分为**行内元素、块状元素和行内块状元素**三种。首先需要说明的是，这三者是**可以互相转换**的，使用display属性能够将三者任意转换：
- (1)display:inline;转换为行内元素

- (2)display:block;转换为块状元素

- (3)display:inline-block;转换为行内块状元素

## 1.行内元素
行内元素最常使用的就是`span`，其他的只在特定功能下使用，修饰字体`<b>`和`<i>`标签，还有`<sub>`和`<sup>`这两个标签可以直接做出平方的效果，而不需要类似移动属性的帮助，很实用。

行内元素特征：
- (1)设置宽高无效
- (2)对margin仅设置左右方向有效，上下无效；padding设置上下左右都有效，即会撑大空间,行内元素尺寸由内含的内容决定，盒模型中 padding,border 与块级元素并无差异，都是标准的盒模型，但是margin却只有水平方向的值，垂直方向并没有起作用。行内元素的水平方向的`padding-left`,`padding-right`,`margin-left`,`margin-right` 都产生边距效果，但是竖直方向的`padding-top`,`padding-bottom`,`margin-top`,`margin-bottom`都会产生边距效果。padding设置上下左右都有效，即会撑大空间但是不会产生边距效果。
- (3)不会自动进行换行
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style type="text/css">
        div {
            display: inline;
        }
        .div1 {
            width: 100px;
            height: 100px;
            background-color: pink;
        }
        .div2 {
            width: 100px;
            height: 100px;
            background-color: #FF8500;
        }
    </style>
 
</head>
<body>
    <div class="div1">123</div>
    <div class="div2">13</div>
</body>
</html>
```
显示：
![image text](https://img-blog.csdn.net/20181017133708398?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3lvdWd1YW5nXw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

（显示的宽高与内容有关，其本身没有width、height）

## 2.块状元素
块状元素代表性的就是`div`，其他如`p`、`nav`、`aside`、`header`、`footer`、`section`、`article`、`ul-li`、`address`等等，都可以用div来实现。不过为了可以方便程序员解读代码，一般都会使用特定的语义化标签，使得代码可读性强，且便于查错。
块状元素特征：
- (1)能够识别宽高
- (2)margin和padding的上下左右均对其有效
- (3)可以自动换行
- (4)多个块状元素标签写在一起，默认排列方式为从上至下

## 3.行内块状元素
>行内块状元素综合了行内元素和块状元素的特性，但是各有取舍。因此行内块状元素在日常的使用中，由于其特性，使用的次数也比较多。
行内块状元素特征：
- (1)不自动换行
- (2)能够识别宽高
- (3)默认排列方式为从左到右
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style type="text/css">
        div {
            display: inline-block;
        }
        .div1 {
            width: 100px;
            height: 100px;
            background-color: pink;
        }
        .div2 {
            width: 100px;
            height: 100px;
            background-color: #FF8500;
        }
    </style>
 
</head>
<body>
    <div class="div1">123</div>
    <div class="div2">13</div>
</body>
</html>
```
显示：
![image text](https://img-blog.csdn.net/20181017133540264?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3lvdWd1YW5nXw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

（宽高与盒子大小有关）

## 4 . 行内元素与块级元素有什么不同？

- 区别一： **块级**：块级元素会独占一行，默认情况下宽度自动填满其父元素宽度 **行内**：行内元素不会独占一行，相邻的行内元素会排在同一行。其宽度随内容的变化而变化。

- 区别二：**块级**：块级元素可以设置宽高 **行内**：行内元素不可以设置宽高

- 区别三： **块级**：块级元素可以设置margin，padding **行内**：行内元素水平方向的`margin-left; margin-right; padding-left; padding-right`;可以生效。但是竖直方向的margin-bottom; `margin-top; padding-top; padding-bottom`;却不能生效。

- 区别四： **块级**：display:block; **行内**：display:inline; 可以通过修改display属性来切换块级元素和行内元素


————————————————
参考文章
CSDN博主「_有光」的原创文章
原文链接：https://blog.csdn.net/youguang_/article/details/83108261