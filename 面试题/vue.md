v-bind:[a]='value'(动态参数)
修饰符:以半角句号 . 指明的特殊后缀，用于指出一个指令应该以特殊方式绑定。
.prevent 修饰符告诉 v-on 指令对于触发的事件调用 event.preventDefault()：
```
<form v-on:submit.prevent="onSubmit">...</form>
```
v-bind缩写 ：
 v-on缩写 @
文字反转：mssage.split('').reverse().join('')

计算属性computed：
1、计算属性和函数
- 计算属性调用：
```
<p>{{getMsg}}</p>
computed:{//计算属性的getter方法
    getMsg:function(){
    return this.msg,split('').reverse().join('')
  }
}
```
- 函数达到同样的效果
```
<p>{{getMsg()}}</p>
method：{
  getMsg:function(){
    return this.msg,split('').reverse().join('')
    }
  }
```
上两种方式的不同：计算属性是计与他们的响应式依赖进行缓存的，只有在相关响应式依赖发生改变时才会重新求值，但是函数不管变化还是不变化都需要重新执行（计算属性适合开销比较大的计算，不然重复执行会开销大的计算会导致性能变差）

2、计算属性和监听属性
- 监听属性(一个方法只能)
```
<div>{{fullName}}</div>
data:{
    firstName:'foo',
    lastName:'bar',
    fullName:'foo bar'
}
watch:{
  firstName:function(val){
    this.fullName=val+''+this.lastName
    }
  }
  lastNam:function(val){
    this.fullName=this.firstName+''+lastName
  }
}
```
- 计算属性
```
computed:function(){
  return this.firstName+''+this.lastName
  }
```

3、计算属性默认只有getter属性，也可以有setter属性(变化时先set修改数据之后再get)
```
computed: {
  fullName: {
    // getter
    get: function () {
      return this.firstName + ' ' + this.lastName
    },
    // setter
    set: function (newValue) {
      var names = newValue.split(' ')
      this.firstName = names[0]
      this.lastName = names[names.length - 1]
    }
  }
}
```

## class与style绑定
```
<div
  class="static"
  v-bind:class="{ active: isActive, 'text-danger': hasError }"
></div>
data: {
  isActive: true,
  hasError: false
}
```
数组语法
```
<div v-bind:class="[activeClass, errorClass]"></div>
data: {
  activeClass: 'active',
  errorClass: 'text-danger'
}
三元表达式
<div v-bind:class="[isActive ? activeClass : '', errorClass]"></div>
对象语法
<div v-bind:class="[{ active: isActive }, errorClass]"></div>
```
用在组件身上也同样适用
```
<my-component v-bind:class="{ active: isActive }"></my-component>
```
style和class用法相同，但是当样式需要添加浏览器引擎前缀的时候与，vue会自动侦测并添加相应的前缀。
```
<div :style="{ display: ['-webkit-box', '-ms-flexbox', 'flex'] }"></div>
```
## 条件渲染
### v-if（条件渲染。不会被渲染保存）优先使用（频繁切换开销高）
在<template>元素上使用v-if，可以把template当成ige不可见的包裹元素，渲染结果不包括
### key管理可复用的元素
因为两个模板使用了相同的元素，<input> 不会被替换掉——仅仅是替换了它的 placeholder。加入可key就不会了
```
<template v-if="loginType === 'username'">
  <label>Username</label>
  <input placeholder="Enter your username" key="username-input">
</template>
<template v-else>
  <label>Email</label>
  <input placeholder="Enter your email address" key="email-input">
</template>
```
### v-show(非条件渲染。会被渲染保存)不建议使用（初始渲染开销高）
v-show 不支持 <template> 元素，也不支持 v-else
> v-if和v-for同事使用时，v-for具有更高的优先级


## 数组方法
- 变更方法
push()：添加
pop()：抛出
shift()：移除数组第一项
unshift()：新项目添加到数组开头并返回长度
splice()：array.splice(index（指定位置）, howmany（删除项目数）, item1, ....., itemX（新增项目内容)
sort()：对数组进行排序
reverse()：反转数组内容
- 替换数组
filter():筛选
concat()：拼接
slice()：返回数组被选中的元素（array.slice(start, end)）可使用负数标识从末尾开始进行选择

## 事件修饰符
v-on提供了事件修饰符
.stop：组织单击事件继续传s
.prevent：提交事件不再重载页面
.capture：事件捕捉，内部元素触发的事件先处理然后才交内部元素处理
.self：当前元素自生触发时处理函数
.once：点击事件只触发一次
.passive：默认行为回立即触发

## 按键修饰符
<input v-on:keyup.enter="submit">
.enter
.tab
.delete (捕获“删除”和“退格”键)
.esc
.space
.up
.down
.left
.right
## 系统修饰符
.ctrl
.alt
.shift
.meta
```
<!-- Ctrl + Click -->
<div v-on:click.ctrl="doSomething">Do something</div>
```

##  .exact 修饰符允许你控制由精确的系统修饰符组合触发的事件。

## 鼠标按钮修饰符
.left
.right
.middle

## 组件
### 监听子组件事件
子组件：
```
<button v-on:click="$emit('enlarge-text')">
  Enlarge text
</button>
```
父组件
```
<blog-post
  ...
  v-on:enlarge-text="postFontSize += 0.1"
></blog-post>
```
### 用事件抛出一个值
```
<button v-on:click="$emit('enlarge-text', 0.1)">
  Enlarge text
</button>
```
```$event抛出值
<blog-post
  ...
  v-on:enlarge-text="postFontSize += $event"
></blog-post>
```
```方法
<blog-post
  ...
  v-on:enlarge-text="onEnlargeText"
></blog-post>
methods: {
  onEnlargeText: function (enlargeAmount) {
    this.postFontSize += enlargeAmount
  }
}
```

组件绑定
```
<blog-post v-bind="post"></blog-post>
```
相当于
```
<blog-post
  v-bind:id="post.id"
  v-bind:title="post.title"
></blog-post>
```

## 插槽
```
<slot name="xxx"></slot>
使用
<tempate v-slot:xxx></template>
缩写
<tempate #xxx></template>
```

## 访问根组件实例
// Vue 根实例
new Vue({
  data: {
    foo: 1
  },
  computed: {
    bar: function () { /* ... */ }
  },
  methods: {
    baz: function () { /* ... */ }
  }
})
访问
this.$root.foo
this.$root.bar
this.$root.baz()
写入
this.$root.foo=2

## 子组件访问父组件的实例
this.$parent访问父组件
this.$parent.$parent访问父组件的父组件

## 父组件调用子组件的方法
使用组件
<input ref="xxx"></input>
使用方法
this.$refs.xxx.onfocus()



## 混入
```
// 定义一个混入对象
var myMixin = {
  created: function () {
    this.hello()
  },
  methods: {
    hello: function () {
      console.log('hello from mixin!')
    }
  }
}

// 定义一个使用混入对象的组件
var Component = Vue.extend({
  mixins: [myMixin]
})

var component = new Component() // => "hello from mixin!"
```
### 选项合并
当混入的对象含有同名原想时，这些选项会呗递归合并，发生冲突时组件数据优先.同名的钩子合并为一个数组，都会被调用，混入的狗子将在组件自身之前调用
```
var mixin = {
  data: function () {
    return {
      message: 'hello',
      foo: 'abc'
    }
  }
 created: function () {
    console.log('混入对象的钩子被调用')
  }
}

new Vue({
  mixins: [mixin],
  data: function () {
    return {
      message: 'goodbye',
      bar: 'def'
    }
  },
  created: function () {
    console.log(this.$data)
    // => {混入对象的钩子被调用 message: "goodbye", foo: "abc", bar: "def" }
  }
})
```

### 全局混入（混入后会影响每一个创建的vue实例）
```
// 为自定义的选项 'myOption' 注入一个处理器。
Vue.mixin({
  created: function () {
    var myOption = this.$options.myOption
    if (myOption) {
      console.log(myOption)
    }
  }
})

new Vue({
  myOption: 'hello!'
})
// => "hello!"
```

## 自定义指令
```
// 注册一个全局自定义指令 `v-focus`
Vue.directive('focus', {
  // 当被绑定的元素插入到 DOM 中时……
  inserted: function (el) {
    // 聚焦元素
    el.focus()
  }
})
//注册一个局部指令
directives: {
  focus: {
    // 指令的定义
    inserted: function (el) {
      el.focus()
    }
  }
}
```
一个指令定义对象可以提供以下钩子函数
bind：只调用一次，指令第一次绑定到元素时调用。在这里可以进行一次性的初始化设置。

inserted：被绑定元素插入父节点时调用 (仅保证父节点存在，但不一定已被插入文档中)。

update：所在组件的 VNode 更新时调用，但是可能发生在其子 VNode 更新之前。指令的值可能发生了改变，也可能没有。但是你可以通过比较更新前后的值来忽略不必要的模板更新 (详细的钩子函数参数见下)。

componentUpdated：指令所在组件的 VNode 及其子 VNode 全部更新后调用。

unbind：只调用一次，指令与元素解绑时调用。

钩子函数参数
指令钩子函数会被传入以下参数：

el：指令所绑定的元素，可以用来直接操作 DOM。
binding：一个对象，包含以下 property：
name：指令名，不包括 v- 前缀。
value：指令的绑定值，例如：v-my-directive="1 + 1" 中，绑定值为 2。
oldValue：指令绑定的前一个值，仅在 update 和 componentUpdated 钩子中可用。无论值是否改变都可用。
expression：字符串形式的指令表达式。例如 v-my-directive="1 + 1" 中，表达式为 "1 + 1"。
arg：传给指令的参数，可选。例如 v-my-directive:foo 中，参数为 "foo"。
modifiers：一个包含修饰符的对象。例如：v-my-directive.foo.bar 中，修饰符对象为 { foo: true, bar: true }。
vnode：Vue 编译生成的虚拟节点。移步 VNode API 来了解更多详情。
oldVnode：上一个虚拟节点，仅在 update 和 componentUpdated 钩子中可用。
除了 el 之外，其它参数都应该是只读的，切勿进行修改。如果需要在钩子之间共享数据，建议通过元素的 dataset 来进行。