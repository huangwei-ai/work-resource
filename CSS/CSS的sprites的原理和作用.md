相信大家和我一样之前都没听过sprites这个东西吧，我也是在一次面试题中遇到，这个问题经常会出现在企业面试题中。

**CSS Sprites**在国内很多人叫**css精灵**，是一种**网页图片应用处理方式**。它**允许你将一个页面涉及到的所有零星图片都包含到一张大图中去**，这样一来，当访问该页面时，载入的图片就不会像以前那样一幅一幅地慢慢显示出来了。对于当前网络流行的速度而言，不高于200KB的单张图片的所需载入时间基本是差不多的，所以无需 顾忌这个问题。

　　**加速的关键**，不是降低重量，而是**减少个数**。传统切图讲究精细，图片**规格越小越好**，**重量越小越好**，其实规格大小无所谓，计算机统一都按byte计算。客户端每显示一张图片都会向服务器发送请求，所以，图片越多请求次数越多，**造成延迟的可能性也就越大**。

## CSS Sprites原理

　　CSS Sprites其实就是**把网页中一些背景图片整合到一张图片文件中**，再利用CSS的“**background-image**”，“**background- repeat**”，“**background-position**”的组合进行背景定位，background-position可以用数字能精确的定位出背景图片的位置。

## CSS Sprites优点

　　利用CSS Sprites能很好地**减少了网页的http请求**，从而大大的**提高了页面的性能**，这也是CSS Sprites最大的优点，也是其被广泛传播和应用的主要原因；
　　CSS Sprites能**减少图片的字节**，曾经比较过多次**3张图片合并成1张图片的字节总是小于这3张图片的字节总和**。

## CSS Sprites缺点
　　诚然CSS Sprites是如此的强大，但是也存在一些不可忽视的缺点
　　在图片合并的时候，你要把多张图片有序的合理的合并成一张图片，还要**留好足够的空间**，**防止板块内不会出现不必要的背景**；这些还好，最痛苦的是在**宽屏，高分辨率的屏幕下的自适应页面**，你的图片如果不够宽，很容易出现**背景断裂**；
　　CSS Sprites在开发的时候比较麻烦，你要通过**photoshop或其他工具测量计算每一个背景单元的精确位置**，这是针线活，没什么难度，但是很繁琐；幸好腾讯的鬼哥用RIA开发了一个**CSS Sprites 样式生成工具**（以后用到的时候要记得），虽然还有一些使用上的不灵活，但是已经比photoshop测量来的方便多了，而且样式直接生成，复制，拷贝就OK！

　　CSS Sprites在**维护的时候比较麻烦**，如果页面背景有少许改动，一般就要改这张合并的图片，无需改的地方最好不要动，这样避免改动更多的css，如果在原来的地方放不下，又只能（最好）往下加图片，这样图片的字节就增加了，还要改动css。

　　CSS Sprites非常值得学习和应用，特别是**页面有一堆ico（图标）**。总之很多时候大家要权衡一下利弊，再决定是不是应用CSS Sprites。

## CSS Sprite的使用

　　有几篇关于CSS Sprites的文章，基本上把其原理和机制说明得很清楚。
　　What Are CSS Sprites?
　　How to create CSS sprites
　　Creating Rollover Effects with CSS Sprites
　　Building a Dynamic Banner with CSS Sprites
　　High Performance Web Sites中关于CSS Sprites的内容3.2. CSS Sprites

## CSS Sprite的例子
　**1. 图片限制(Image Slicing)[1]**
　　典型如文本编辑器，小图标特别多，打开时一张张跑出来，给用户的感觉很不好。如果能用一张图解决，则不会有这个问题，比如百度空间、163博客、Gmail都是这么做的。
　　Image Slicing’s Kiss of Death
　　http://www.alistapart.com/articles/sprites
**2. 单图转滚(Single-image Rollovers)[1]**
　　触发切换图片的需求，传统方案得重新请求新图片，因为网络问题经常造成停留或等待。如果能把多种状态合并成一张图，就能完美解决，然后再使用背景图技术模拟动态效果。
　　ColorScheme Ratings
　　http://demo.rexsong.com/200608/colorscheme_ratings/
**3. 延长背景(Extend Background Image)[1]**
　　如果图片的某边可以背景平铺无限延长，则不需要每个角、每条边单独搞出来，图片能少一个就少一个。其实，这个理论还可以扩展到四角容器里，好处是能大大简化HTML Structure。
　　Extend Background Image
　　 http://demo.rexsong.com/200705/extend_background_image/
　　综合案例
　　Google Korea（1和2技巧）
　　http://demo.rexsong.com/200705/google_korea/
　　CSS Menus（2和3技巧）
　　 http://demo.rexsong.com/200705/css_background_menus/

## CSS Sprites的问题

　　由于IE6存在的**background的flicker问题**IE6/Win, background image on , cache=‘check every visit’: flicker!，有人针对此问题提出了解决方案Fast Rollovers Without Preload
　　关于IE6的flicker问题，fivesevensix.com上有一篇很不错的研究文章Minimize Flickering CSS Background Images in IE6
　　另外：brunildo.org的CSS tests and experiments是关于css各种功能不错的参考手册和测试工具。

**图片优化**

   1. 对于非动画的GIF更建议使用PNG8因为它同样能做到一样的效果，而且能为你节省10%-30%的文件体积。
   2. Photoshop相比起Fireworks，导出同等质量的PNG图片，体积会稍大。而Fireworks虽然做了相应压缩优化，但没有达到最优秀的压缩。
   3. 我所知的设计软件，对于PNG图片的处理都没做到最优秀的压缩，图片体积还有一定的压缩空间。可以尝试使用下面介绍的”图像优化工具” 做无失真的压缩优化。
   4. 图片体积及尺寸方面，建议体积保持在100K以内(较为符合国情最佳请求SIZE)，size为800px(最佳尺寸)。(从某权威人事中得知，具体无从考证)

## CSS Sprites图片切割术

   1. CSS Sprites图片顺序合图片由上至下、左至右添加。而background-position一般采用数字组合形式定位，这样能减少维护带来的不必要麻烦。
   2. 不建议CSS Sprites图片中保持一定的间距，因为文件size增大而增加文件体积。
   3. CSS Sprites图片中把颜色较近或相同的组合在一起可以降低颜色数，因为少色数的图片文件体积会相对的小。
   4. size相同的CSS Sprites图片中留有较大空隙，某程度上多数情况会增大了体积，所以CSS Sprites的图片不要有空隙。
   5. 在size相同的CSS Sprites图片中，垂直排列的图片会比水平排列的文件体积要大。
   6. 在CSS Sprites图片中，水平排列的图片会比垂直排列的文件体积要大。
   7. 图片对等合并：应用CSS Sprites图片时，适当地把对等相同的图像合并，以节省空间及减少体积。
   8. 区分开不需要合并的图像：如当前用户确定只显示一种状态或一个级别时，不必要把其他的级别或状态的图片合并。
   9. 黄金切割位：在CSS Sprites图片的最右或左边为最灵活动位置最适宜摆放文本前的icon，因此不会受到其它CSS Sprites图片干预，也不需要预留一定的行宽。

**补两条**
10. 有的说定位时避免使用bottom或right等，当使用CSS sprite的时候，只用background-position: bottom -300px或background-position: right -200px;非常容易。这刚开始的时候是可行的，但是问题是，当你在宽度上或高度上扩展相关sprite图片的时候，原先设置的位置可能是错的，因为那个图片已经不再Sprite图片的底部或右部了。使用确切的位置来避免这个问题。

其实我感觉一般情况宽度图不片不会改应变，用RIGHT和 LEFT还是挺方便的，但从整体考虑，升级了。改版的。图片宽度还是有可能会改变的。必竟开始时做太宽也没什么好处，还是浪费很多空间。就是多费点时间去对坐标，最好还是不用RIGHT 和 LEIFT的了。

12　有的说竟给每个图片足够的空间
　　就像你在本文顶部的实例图片看到的那样，那些小图片都被预留了足够的空间。为什么不把他们塞到一块来让sprite图片更小呢? 因为使用这些图片的元素通常都会有大量的内容而且可能会需要扩展的间距，以至于其它图片不会意外出现。