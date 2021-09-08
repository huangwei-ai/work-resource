vue组件之间的传值方法是作为一个vue的使用者必会的一个技能，如过你连vue组件之间的传值都没有掌握，那说明你的基础不够好（那当然也在说我，会，但是没有掌握会在面试时吃亏的）

## 父组件向子组件传值
-  1.在使用子组件时用v-bind绑定父组件的数据
```
<com1 :parentmsg="message"></com1>
```
- 2.在子组件中的props数组中定义一下刚绑定的属性名parentmsg才能使用
```
template:'<h1>这是子组件---{{parentmsg}}</h1>',
props:["parentmsg"],
```
props中的数据是父组件传来的，属于只读

例如：
```

<com1 :parentmsg="message"></com1>
 
--------------------------------------------
 
components:{
                com1:{
                    //data数据是组件私有的，可读可写
                    //props数据是父组件传来的，只读
                    data:function(){
                        return {
                            title:'123',
                            content:'qqq'
                        }   
                    },
                    //子组件使用父组件绑定的属性名来获取父组件数据
                    //使用之前必须要在props数组中定义一下才能使用
                    template:'<h1>这是子组件---{{parentmsg}}</h1>',
                    props:["parentmsg"], // 或对应一个对象{parentmsg:string}
                }
            }
```
注：另外一种更简洁的获取父组件数据的方法，直接使用$parent.message

## 父组件向子组件传方法
- 1.在使用子组件时用v-on绑定父组件的方法名
```
<com1 @myfunc='show'></com1>
```
- 2.在子组件中的方法调用this.$emit('myfunc')就能调用父组件的方法
如果父组件方法有参数，直接在'myfunc'后添加参数
```
this.$emit('myfunc',参数1,参数2)
```
例如：
```
<div id="app">
     <com1 @myfunc='show'></com1>
</div>
 
<template id="temp1">
     <div>
         <h1>这是子组件</h1>
         <input type="button" value="子组件触发父组件方法按钮" @click='myclick'>
     </div>
</template>
-----------------------------------------------------------------------------------
<script>
        var vm = new Vue({
            el:'#app',
            data:{},
            methods:{
                show(mydata){
                    console.log('调用了父组件的show()'+mydata)
                }
            },
            components:{
                com1:{
                    template:'#temp1',
                    methods:{
                        myclick(){
                            this.$emit('myfunc',123)
                        }                     
                    }
                }
            }
        })
 ```
## 子组件向父组件传值
方法是：父组件向子组件传一个方法，子组件执行此方法时将自己的数据作为参数返回
```
<div id="app">
    <com1 @myfunc='show'></com1>
</div>
 
<template id="temp1">
     <div>
         <h1>这是小小的子组件</h1>
         <input type="button" value="子组件触发父组件方法按钮" @click='myclick'>
     </div>
</template>
------------------------------------------------------------------------------------
    <script>
        var vm = new Vue({
            el:'#app',
            data:{
                message:'父组件的值',
                sondata:null,
            },
            methods:{
                show(data){
                    this.sondata = data;               
                }
            },
            components:{
                com1:{
                    data:function(){
                        return {
                            son:{name:'小头儿子',age:18}
                        }
                    },
                    template:'#temp1',
                    methods:{
                        myclick(){
                            this.$emit('myfunc',this.son)
                        }                     
                    }
                }
            }
        })
    </script>
```
## 子组件向父组件传方法
使用ref获取Dom元素和组件引用
- 1.用ref给元素绑定
  
```
<h3  ref='myh3'>哈哈哈，今天天气很好！</h3>
```
- 2.用this.$refs.myh3获取此元素

```
console.log(this.$refs.myh3.innerText);
```
父组件可以通过ref来获取子组件的data和方法
```
 <login ref='mylogin'></login>
 ```
- 1.子组件引用时绑定ref属性

- 2.父组件用this.$refs.mylogin来获取子组件data和method
```
console.log(this.$refs.mylogin.message);  //获取子组件message数据
 
this.$refs.mylogin.show();     //执行子组件show()方法
 ```
## 兄弟组件之间的传值（eventBus）
- 第一步：创建一个js文件，eventBus.js，位置随便放，我是放在了src目录下
```
import Vue from 'vue'
export default new Vue()
```
- 第二步：在兄弟组件中引用该js
```
  import eventVue from '../../js/event.js'
```
- 第三步：传输的组件使用$.emit方法传递值
```
eventVue.$emit("add",this.msg)   //$emit这个方法会触发一个事件
```
- 第四步：
```
 //一般写在created里
 eventVue.$on("add",(message)=>{   
      //这里最好用箭头函数，不然this指向有问题
      //这里的message就是兄弟组件传来的值      
 })
 ```
 兄弟组件之间的传值就是用
 ```
 $emit传值、$on监听接收
 ```
## 跨级组件传值
- 1.provide/inject(发布订阅模式)
provide / inject API 主要解决了跨级组件间的通信问题，不过它的使用场景，主要是子组件获取上级组件的状态，跨级组件间建立了一种主动提供与依赖注入的关系。
```
// A.vue
export default {
  provide: {
    name: '浪里行舟'
  }
}
// B.vue
export default {
  inject: ['name'],
  mounted () {
    console.log(this.name);  // 浪里行舟
  }
}
```
- 2.```$attrs/$listeners```
```$attrs``` 里存放的是父组件中绑定的非 Props 属性，$listeners里存放的是父组件中绑定的非原生事件。

————————————————
参考资料
CSDN博主「执梦起航」的原创文章
原文链接：https://blog.csdn.net/dapaoshinidie/article/details/101566027