Scope Hosting也是webpack性能优化的一种方式，不过侧重的是对产出代码的优化。

使用Scope hosting的优点：
- 1、代码体积更小
- 2、创建函数作用域更少
- 3、代码可读性更好

示例说明：

- （1）在A文件导出一个字符串，在B文件中引入之后，通过console.log打印出来。
- （2）通过webpack打包构建之后，会发现将这两个文件构建成了两个函数，一个函数内是我们输出的字符串，一个函数内是console.log语句。
问题：两个函数也就是两个作用域；不仅代码增加了，可读性还不太友好。
- （3）使用scope hosting进行配置处理之后，会讲本身的两个函数作用域合成一个，减少了代码量，也便于阅读代码。
![image text](https://img-blog.csdnimg.cn/20210218100205145.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5MjA3OTQ4,size_16,color_FFFFFF,t_70)
![image text](https://img-blog.csdnimg.cn/20210218100242602.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5MjA3OTQ4,size_16,color_FFFFFF,t_70)

使用Scope Hosting插件 进行配置：
![image text](https://img-blog.csdnimg.cn/20210218100542873.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5MjA3OTQ4,size_16,color_FFFFFF,t_70)
打包产物：
![image text](https://img-blog.csdnimg.cn/20210218100336577.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5MjA3OTQ4,size_16,color_FFFFFF,t_70)


————————————————
参考文章
CSDN博主「祥哥的说」的原创文章
原文链接：https://blog.csdn.net/qq_39207948/article/details/113842168