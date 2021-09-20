## webpack自带的优化
### 优化一：Tree-Shaking
- 在生产环境下
- 使用import引入(不是require)
- 会自动去除没用的代码
举例：
```
//a.js
let add = (a,b) => {
    return a + b + 'add';
}

let minus = (a,b) => {
    return a - b + 'minus';
}

exports default {add,minus}

//index.js
import calc from './a.js'

console.log(calc.add(1,2));
```
>这时候，在生产环境下打包：只会打包add方法，不会打包minus方法。
>这时候，在开发环境下打包：不仅会打包add方法，还会打包minus方法。

1、加入使用require引入
```
//a.js
let add = (a,b) => {
    return a + b + 'add';
}

let minus = (a,b) => {
    return a - b + 'minus';
}

exports default {add,minus}

//index.js
let calc = require('./a.js');

console.log(calc.add(1,2));//这是报错的，因为es6模块会把皆果放到default上

console.log(calc.default.add(1,2);//'3add'
```
2、require，打包，不管在生产还是在开发环境，都会打包add和minus方法
>总结：import在生产环境下，支持tree-shaking，require不支持tree-shaking

语法	| 生产环境 |	开发环境
-|-|-
import	|支持tree-shaking	|不支持
require|	不支持	|不支持

### 优化二：Scope-Hosting
>作用域提升

- 在生产环境下，
- 提升作用域
举例：
```
let a = 1;
let b = 2;
let c = 3;
let d = a + b + c;
console.log(d);
```
>webpack在生产环境下打包的时候，会直接将d打包成a+b+c的结果，即d直接打包成6.这样就无需声明多个变量再去相加。
webpack在生产环境下会自动省略不必要的代码。

————————————————
参考资料
CSDN博主「俞华」的原创文章
原文链接：https://blog.csdn.net/qq_17175013/article/details/87002440