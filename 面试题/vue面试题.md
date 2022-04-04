## 1、v-if和v-show的区别
- 都能控制元素的显示和隐藏
- 实现本质不同：v-show本质是通过css的display设置为none控制隐藏，只会编译一次
- v-if是动态的向DOM树添加或者删除DOM元素，若初始值为false，就不会编译
- v-if不停的销毁和创建比较消耗性能，若需要频繁却换使用v-show

## 2、vue中的data为什么是一个函数
- 函数：每复用一次组件，就会返回一份新得data，类似于给每个组件实例创建一个私有的数据空间，让每个组件实例维护各自的数据
- 对象：组件实例共用一个data，造成一个变了全部都会变的结果
- js本身面向对象变成也是基于原型链和构造函数，要注意原型链上添加一般都是一个函数方法，而不是添加一个对象

## 3、 vuex的action和，mutations的区别
actions
- 用于通过提交mutation改变数据
- 会默认将自身封装成一个promise
- 可以包含任意的异步操作
mutations
- 通过提交commit改变数据
- 只是一个单纯的函数
- 不要使用异步操作，异步操作会导致变量不能追踪

## 4、如何在vuex中使用异步修改
const actions={
  asyncLogin({commit},n){
    return new Promise(resolve=>{
      setTimeout(()=>{
        commit(type.userLogin,n);
        resolve();
      },3000)
    })
  }
}

## 5、vue组件间通信方式
- 父传子：props
```
<!-- 使用——传参 -->
<component :参数="参数内容"/>
//组件注册
- import xxx from
export default{
    name:'id',
    data(){
        return {
            参数内容a:具体数据
        }
    }
    components:{
        //注册组件
        "component":xxx
    }
}

<!-- 使用——接收 -->
export default{
    name:'id',
    props:{
        参数名: {
            type:具体类型,
            require:是否必传,
        }
    }
    <!-- computed数据监听 -->
}
```

- 子传父($emit子传v-on父接收, app.$emit子传app.$on父接收)
```
<!-- $emit使用传参(子组件component) -->
method:{
  xxx(){
    this.$emit('事件名',传递的数据)
  }
}
<!-- v-on接收参数 -->
<component v-on:事件名="updateTitle"/>
updateTitle($event)接受传递过来的数据
```
```
//创建一个空的vue实例作为中央事件总线，触发监听事件，巧妙实现任何组件之间的通信（父子，兄弟，跨级），项目大时可以使用vuex
var App=new Vue()

<!-- $emit使用传参 -->
App.$emit(事件名，数据)

<!-- $on接收参数 -->
App.$on(事件名,data=>{});
```

- vuex和localStorage
1、vuex是vue的状态管理器，存储的数据是响应式的，但是不会保存，刷新之后就回到了初始状态，
具体做法应该在vuex里数据改变的时候把数据拷贝一份保存到localStorag里面，刷新之后，如果localStorage里面有保存数据，再取出来替换state
```
<!-- vuex -->
export default new Vuex.Store({
    state:{
        变量名：变量初始值
    }
    mutation:{
        方法名(state,参数内容){
            state.变量名：参数内容
            try{
                //拷贝一份到loScalStorage里面
                window.localStorage.setItem('变量名',Stringify后的参数内容)
            }
        }
    }
})
```

- attrs/listeners（祖孙传值）

## 6、双向绑定是怎么实现的
- 采用数据劫持结合发布订阅模式
- 通过object.defineProperty()来劫持各个属性的setter，getter
- 数据变动的时候发布消息给订阅者，触发相应的监听回调
```
var obj={};
Object.defineProperty(obj,'name',{
    get:function(){}
    set:function(){}
})
```

## 单页面应用和多页面应用的区别和优缺点
- 单页面应用spa：只有一个主页面的应用，一开始就要加载所有的html，css，js，页面片段分开写，交互的时候由从路由程序动态载入，单页面的页面跳转，仅刷新局部资源，多应用于pc端
- 多页面mpa，一个应用多个页面，页面跳转时候整页刷新

单页面优点：
- 用户体验好，改的时候不需要加载整个页面，服务器压力小
- 前后端分离
- 页面效果比较炫酷（方便添加转场动画）
单页面缺点
- 不利于seo
- 导航不可用，要导航需要自己实现前进后退，建立堆栈管理
- 初次加载消耗多
- 页面复杂度提高


## vue的事件修饰符
.stop:放之事件冒泡，相当于even.stopPropagation()
.prevent:取消该事件,event.preventDefault()
.capture:与冒泡事件相反，事件捕获由外到内
.self：触发范围内的大事件，不包含子元素
.once：只触发一次
.passive：表示监听函数不会调用preventDefault()

## vue的两个核心
- 数据驱动：数据的改变会驱动视图自动更新
- 组件：组件化开发，降低数据之间的耦合度，提高代码的复用性，可重用性


## vue的性能优化方法
1、编译阶段
- 尽量减少data中的数据，data中的数据都会增加getter、setter，会收集对应的watcher
- 如果尽量使用v-for给每一项元素绑定事件时使用事件代理
- SPA页面采用keep-alive缓存组件
- 用v-if代替v-show
- key保持唯一
- 使用路由懒加载、异步组件
- 防抖、节流
- 第三方模块按需引入
- 长列表滚动到可视区域动态加载

2、用户体验
- 骨架屏
- pwa
- 可以使用缓存(客户端缓存，服务端缓存）优化，服务端开启gzip压缩

3、seo优化
- 预渲染
- 服务端渲染SSR

4、打包优化
- 压缩代码
- TreeShaking/Scop Hoisting
- 使用cdn加载第三方模块
- 多线程打包happypack
- splitChunks抽离公共文件
- sourceMap优化

## v-model的实现原理
是value和事件的语法糖
- 可以是value和input、value+change、等事件的组合，进行数据的双向绑定
- v-model内部为不同的输入元素使用不同的属性并抛出不同的事件
- 通过prop和event属性来进行自定义
- this.$emit('change',$event.target.value)

## nextTick的实现原理

在下次DOM更新循环结束之后执行延迟回调。nextTick主要使用了宏任务和微任务根据执行环境分别采用
- promise
- mutationObserve
- setImmediate
-都不行采用setTimeout
定义一个异步方法，多次采用nextTick会将方法存在队列中，通过这个异步方法清空当前队列


## computed和watcher
- computed本质是一个具备缓存的watcher，依赖的属性变化就会更新视图
适用于计算比较消耗性能的计算场景，表达式过于复杂，模板中放入过多逻辑会让模板难以维护，可以将复杂的逻辑放到计算属性中去处理
- watch没有缓存性，观察作用，监听某些数据执行回调
需要深度监听：deep：true
这样会带来性能问题，优化可以使用字符串监听，如果没有写到组件中，不要忘记使用unWatch手动注销