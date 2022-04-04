## Vuex是什么？
Vuex 是一个专为 Vue.js 应用程序开发的**状态管理模式**。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化

>每一个 Vuex 应用的核心就是 store（仓库）。“store”基本上就是一个容器，它包含着你的应用中大部分的状态 (state)。Vuex 和单纯的全局对象有以下两点不同：

- Vuex 的状态存储是响应式的。当 Vue 组件从 store 中读取状态的时候，若 store 中的状态发生变化，那么相应的组件也会相应地得到高效更新。
- 你不能直接改变 store 中的状态。改变 store 中的状态的唯一途径就是显式地提交 (commit) mutation。这样使得我们可以方便地跟踪每一个状态的变化，从而让我们能够实现一些工具帮助我们更好地了解我们的应用。


## 实现简易版的vuex
先来看下vuex的基本用法
```
import Vue from 'vue';
import Vuex from 'vuex';
// 1.Vue.use(Vuex);  Vuex是一个对象 install方法
// 2.Vuex中有一个Store类 
// 3.混入到组件中 增添store属性

Vue.use(Vuex); // 使用这个插件  内部会调用Vuex中的install方法

const store = new Vuex.Store({
  state:{ // -> data
    age:10
  },
  getters:{ // 计算属性
    myAge(state){
      return state.age + 20
    }
  },
  mutations:{ // method=> 同步的更改state  mutation的参数是状态
    changeAge(state,payload){
      state.age += payload; // 更新age属性
    }
  },
  actions:{ // 异步操作做完后将结果提交给mutations
    changeAge({commit},payload){
      setTimeout(() => {
        commit('changeAge',payload)
      }, 1000);
    }
  }
});
export default store;
```
通过用法可以知道：

- Vuex是一个对象，它作为vue的插件，必然有install方法；
- Vuex中有一个Store类，在使用的时候有使用new;
- 需要将store混入到组件中。


### 于是可以梳理好入口文件
#### vuex/index.js
```
import { Store, install } from './store'; 

// 这个文件是入口文件，核心就是导出所有写好的方法
export default {
  Store,
  install
}
```
### store文件
#### vuex/store.js
```
export let Vue;

export class Store {
  
}

// _vue 是Vue的构造函数
export const install = (_vue) => {
  // 需要保存Vue,用户传入的Vue构造函数
  Vue = _vue; 
}
```
接下来就是把store挂载到每个组件上面，这样数据才能互通共享，很显然，通过Vue.mixin 在Vue生命周期beforeCreate 可以为每个组件注入store；
```
import applyMixin from "./mixin";
export let Vue;

export class Store {
  
}

// _vue 是Vue的构造函数
export const install = (_vue) => {
  // 需要保存Vue,用户传入的Vue构造函数
  Vue = _vue;
  // 需要将根组件中注入的store 分派给每一个组件 （子组件） Vue.mixin
  applyMixin(Vue);
}
```
#### vuex/mixin.js
```
export default function applyMixin(Vue) {
  // 父子组件的beforecreate执行顺序
  Vue.mixin({ // 内部会把生命周期函数 拍平成一个数组 
    beforeCreate: vuexInit
  });
}

// 组件渲染时从父=》子

function vuexInit() {
  // 给所有的组件增加$store 属性 指向我们创建的store实例
  const options = this.$options; // 获取用户所有的选项
  if (options.store) { // 根实例（只有根实例才会有store属性）
    this.$store = options.store;
  } else if (options.parent && options.parent.$store) { // 儿子 或者孙子....
    // 后面的每一个都从其父组件拿到store
    this.$store = options.parent.$store;
  }
}
```
## 接下来就是处理state,getters,mutations,actions

### state实现
```
export class Store {
  constructor(options) {
    const state = options.state; //数据变化要更新视图 （vue的核心逻辑依赖收集）
    this._vm = new Vue({
      data: { // 属性如果是通过$开头的 默认不会将这个属性挂载到vm上
        $$store: state
      }
    })
  }
  get state() { // 属性访问器   new Store().state  Object.defineProperty({get()})
    return this._vm._data.$$state
  }
  
}
```
首先来处理state,options是用户传入的，其中有state,getters,mutations,actions，自然可以在options.state中取到，但是此时state还不是响应式，可以借助new Vue中data的数据是响应式处理这个问题，将state挂载到$$state上，这个属性是不会被vue暴露出去（可能是内部做了处理）。当我们在组件中去获取值的时候，比如this.store.state 就走到到了访问器get state() 就会将整个仓库的state返回出去，而且数据是响应式的。至于为什么在_vm._data上，需要去看下vue源码实现。

### getters实现
```
export class Store {
  constructor(options) {
    // 1.处理state
    const state = options.state; //数据变化要更新视图 （vue的核心逻辑依赖收集）
    this._vm = new Vue({
      data: { // 属性如果是通过$开头的 默认不会将这个属性挂载到vm上
        $$store: state
      }
    })
    
    // 2.处理getters属性 具有缓存的 computed 带有缓存 （多次取值是如果值不变是不会重新取值）
    this.getters = {};
    Object.key(options.getters).forEach(key => {
      Object.defineProperty(this.getters, key, {
        get: () => options.getters[key](this.state)
      })
    })
  }
  get state() { // 属性访问器   new Store().state  Object.defineProperty({get()})
    return this._vm._data.$$state
  }

}
```
通过循环用户传进来的getters,再通过Object.defineProperty把每一个getter放入store中。不过目前每一次取值都会重新计算，没有缓存功能，不符合vue计算属性的用法以及定义。

先来改造下对象遍历这个方法，因为这个方法后面用的比较多。

### vuex/util.js
```
export const forEachValue = (obj, callback) => {
  Object.keys(obj).forEach(key => callback(obj[key], key))
}

export class Store {
  constructor(options) {
    // 1.处理state
    const state = options.state; //数据变化要更新视图 （vue的核心逻辑依赖收集）
    this._vm = new Vue({
      data: { // 属性如果是通过$开头的 默认不会将这个属性挂载到vm上
        $$store: state
      }
    })

    // 2.处理getters属性 具有缓存的 computed 带有缓存 （多次取值是如果值不变是不会重新取值）
    this.getters = {};
    forEachValue(options.getters, (fn, key) => {
      Object.defineProperty(this.getters, key, {
        get: () => fn(this.state)
      })
    })
  }
  get state() { // 属性访问器   new Store().state  Object.defineProperty({get()})
    return this._vm._data.$$state
  }

}
```
逻辑都是一样的，接着处理下缓存功能。
```
export class Store {
  constructor(options) {
    // 1.处理state
    const state = options.state; //数据变化要更新视图 （vue的核心逻辑依赖收集）
    const computed = {};

    // 2.处理getters属性 具有缓存的 computed 带有缓存 （多次取值是如果值不变是不会重新取值）
    this.getters = {};
    forEachValue(options.getters, (fn, key) => {
      // 将用户的getters 定义在实例上， 计算属性是如何实现缓存
      computed[key] = () => fn(this.state);
      // 当取值的时候执行计算属性的逻辑，此时就有缓存功能
      Object.defineProperty(this.getters, key, {
        get: () => fn(this._vm[key])
      })
    })

    this._vm = new Vue({
      data: { // 属性如果是通过$开头的 默认不会将这个属性挂载到vm上
        $$store: state
      },
      computed,
    })

  }
  get state() { // 属性访问器   new Store().state  Object.defineProperty({get()})
    return this._vm._data.$$state
  }

}
```
computed具有缓存功能，可以在用户传入的getters的时候，将用户的getters 定义在实例上，computed[key] = () => fn(this.state) ，在取值的时候fn(this._vm[key])执行计算属性的逻辑。vuex的作者真是脑洞大开，鬼才啊，这都能想到。

mutation 都有一个字符串的 事件类型 (type) 和 一个 回调函数 (handler)

- 对传入的属性进行遍历订阅

- 通过commit方法触发调用。

### mutation实现
```
// 3.实现mutations
this.mutations = {};
forEachValue(options.mutations, (fn, key) => {
  this.mutations[key] = (payload) => fn(this.state, payload)
})

commit = (type, payload) => { //保证当前this 当前store实例
    this.mutations[type](payload)
}
```
>commit使用箭头函数是为了保证调用的都是当前实例，一是通过this.commit(type,data)，二是在action中被解构使用changeAge({commit},payload){}

actions和dispath也是如此。

### 完整的Store类
```
export class Store {
  constructor(options) {
    // 1.处理state
    const state = options.state; //数据变化要更新视图 （vue的核心逻辑依赖收集）
    const computed = {};

    // 2.处理getters属性 具有缓存的 computed 带有缓存 （多次取值是如果值不变是不会重新取值）
    this.getters = {};
    forEachValue(options.getters, (fn, key) => {
      // 将用户的getters 定义在实例上， 计算属性是如何实现缓存
      computed[key] = () => fn(this.state);
      // 当取值的时候执行计算属性的逻辑，此时就有缓存功能
      Object.defineProperty(this.getters, key, {
        get: () => fn(this._vm[key])
      })
    })

    this._vm = new Vue({
      data: { // 属性如果是通过$开头的 默认不会将这个属性挂载到vm上
        $$store: state
      },
      computed,
    })

    // 3.实现mutations
    this.mutations = {};
    forEachValue(options.mutations, (fn, key) => {
      this.mutations[key] = (payload) => fn(this.state, payload)
    })

    // 4.实现actions
    this.actions = {};
    forEachValue(options.actions, (fn, key) => {
      this.actions[key] = (payload) => fn(this, payload);
    });

  }

  commit = (type, payload) => { //保证当前this 当前store实例
    this.mutations[type](payload)
  }
  
  dispatch = (type, payload) => {
    this.mutations[type](payload)
  }

  get state() { // 属性访问器   new Store().state  Object.defineProperty({get()})
    return this._vm._data.$$state
  }

}
```
### 完整的store.js
```
import applyMixin from "./mixin";
import { forEachValue } from './util';
export let Vue;

export class Store {
  constructor(options) {
    // 1.处理state
    const state = options.state; //数据变化要更新视图 （vue的核心逻辑依赖收集）
    const computed = {};

    // 2.处理getters属性 具有缓存的 computed 带有缓存 （多次取值是如果值不变是不会重新取值）
    this.getters = {};
    forEachValue(options.getters, (fn, key) => {
      // 将用户的getters 定义在实例上， 计算属性是如何实现缓存
      computed[key] = () => fn(this.state);
      // 当取值的时候执行计算属性的逻辑，此时就有缓存功能
      Object.defineProperty(this.getters, key, {
        get: () => fn(this._vm[key])
      })
    })

    this._vm = new Vue({
      data: { // 属性如果是通过$开头的 默认不会将这个属性挂载到vm上
        $$store: state
      },
      computed,
    })

    // 3.实现mutations
    this.mutations = {};
    forEachValue(options.mutations, (fn, key) => {
      this.mutations[key] = (payload) => fn(this.state, payload)
    })

    // 4.实现actions
    this.actions = {};
    forEachValue(options.actions, (fn, key) => {
      this.actions[key] = (payload) => fn(this, payload);
    });

  }

  commit = (type, payload) => { //保证当前this 当前store实例
    this.mutations[type](payload)
  }
  
  dispatch = (type, payload) => {
    this.mutations[type](payload)
  }

  get state() { // 属性访问器   new Store().state  Object.defineProperty({get()})
    return this._vm._data.$$state
  }

}

// _vue 是Vue的构造函数
export const install = (_vue) => {
  // 需要保存Vue,用户传入的Vue构造函数
  Vue = _vue;
  // 需要将根组件中注入的store 分派给每一个组件 （子组件） Vue.mixin
  applyMixin(Vue);
}
```
简易版的vuex到此完成。接下来就是要处理module。

### 完整版Vuex实现
我们实现了一个简易版的Vuex，对state,actions,mutations,getters 进行了功能的实现。但是没有对modules进行处理，其实modules才是Vuex中最核心并且是最难实现的。

>Vuex 允许我们将 store 分割成大大小小的对象，每个对象也都拥有自己的 state、getter、mutation、action，这个对象我们把它叫做 module（模块），在模块中还可以继续嵌套子模块。

state: 所有模块中的state中数据最终都会嵌套在一棵树上。类似于如下
![节点](image/acton.jpg)
模块内部的 action、mutation 和 getter 默认可是注册在全局命名空间的，这样使得多个模块能够对同一 mutation 或 action 作出响应。因此在订阅mutation 和action时必须存储在数组中，每次触发，数组中的方法都要执行。

Vuex中可以为每个模块添加namespaced: true来标记为当前模块划分一个命名空间，接下来看下具体怎么实现一个完整的Vuex。

## 具体实现
总体思路可以分为以下：

- 模块收集。就是把用户传给store的数据进行格式化，格式化成我们想要的结构（树）
- 安装模块。需要将子模块通过模块名定义在跟模块上
- 把状态state和getters定义到当前的vm上。
### 模块收集
```
import ModuleCollection from './module/module-collection'
export let Vue;

export class Store {
  constructor(options) {
    
    const state = options.state; //数据变化要更新视图 （vue的核心逻辑依赖收集）

    // 1.模块收集
    this._modules = new ModuleCollection(options);

  }
}
```
ModuleCollection 类的实现

这个类是收集模块，格式化数据用的，那我们先要知道需要什么样的格式。
```
this.root = {
   _raw: '根模块',
    _children:{
      a:{
          _raw:"a模块",
          _children:{
              c:{
                  .....
              }
          },
          state:'a的状态'  
        },
        b:{
          _raw:"b模块",
          _children:{},
         state:'b的状态'  
        }
    },
    state:'根模块自己的状态'
 }
 ```
最终需要的是这样一个数结构。
```
export default class ModuleCollection {
  constructor(options) {
    // 注册模块 需要用到栈结构数据，[根，a],每次循环递归的时候将其入栈。这样每个模块可以清楚的知道自己的父级是谁
    this.register([], options)
  }

  
  register(path, rootModule) {
    // 格式化后的结果
    let newModule = { 
      _raw: rootModule, // 用户定义的模块
      _children: {}, // 模块的儿子
      state: {} // 当前模块的状态
    }
    
    
    if (path.length === 0) { // 说明是根模块
      this.root = newModule
    } 
    
    // 用户在模块中传了modules属性
    if (rootModule.modules) {
      // 循环模块 module模块的定义 moduleName模块的名字
      forEachValue(rootModule.modules, (module, moduleName) => {
        this.register(path.concat(moduleName), module)
      })
    }
    
  }
}
```
第一次进来的时候path是空数组，root就是用户传进去的模块对象；如果模块有modules属性，需要循环去注册这个模块。path.concat(moduleName) 就返回了[a,c]类似的格式。 接下来看下path不为空的时候
```
if (path.length === 0) { // 说明是根模块
  this.root = newModule
} else {
  // this.register(path.concat(moduleName), module); 递归注册前会把module 的名放在 path的位
  this.root._children[path[path.length -1]] = newModule
}
```
path[path.length -1] 可以取到最后一项，也就是模块的儿子模块。这里我们用的是this.root._children[path[path.length -1]] = newModule。这样写会把有多层路径的模块最后一项也提到和它平级，因此需要确定这个模块的父级是谁，再把当前模块挂到父级就okl了
```
if (path.length === 0) { // 说明是根模块
  this.root = newModule
} else {
  // this.register(path.concat(moduleName), module); 递归注册前会把module 的名放在 path的位
  // path.splice(0, -1) 是最后一项，是需要被挂的模块
  let parent = path.splice(0, -1).reduce((memo, current) => {
    return memo._children[current];
  }, this.root);
  parent._children[path[path.length - 1]] = newModule
}
```
### 模块的安装
将所有module收集后需要对收集到数据进行整理

- state数据要合并。 通过Vue.set(parent,path[path.length-1],rootModule.state)，既可以合并，又能使使 module数据成为响应式数据;
- action 和mutation 中方法订阅（数组）
```
// 1.模块收集
this._modules = new ModuleCollection(options);

// 2.安装模块 根模块的状态中 要将子模块通过模块名 定义在根模块上
installModule(this, state, [], this._modules.root);
this就是store, 需要完成installModule方法。installModule中传入的有当前模块，这个模块可能有自己的方法。为此先改造下代码，创建Module类。

import { forEachValue } from '../util';

class Module {
  get namespaced() {
    return !!this._raw.namespaced
  }

  constructor(newModule) {
    this._raw = newModule;
    this._children = {};
    this.state = newModule.state
  }

  getChild(key) {
    return this._children[key];
  }

  addChild(key, module) {
    this._children[key] = module
  }

  // 给模块继续扩展方法
  
}

export default Module;
```
ModuleCollection中相应的地方稍作修改。
```
import Module from './module'

export default class ModuleCollection {
  constructor(options) {
    // 注册模块 需要用到栈结构数据，[根，a],每次循环递归的时候将其入栈。这样每个模块可以清楚的知道自己的父级是谁
    this.register([], options)
  }

  register(path, rootModule) {
    // 格式化后的结果
    let newModule = new Module(rootModule)

    if (path.length === 0) { // 说明是根模块
      this.root = newModule
    } else {
      // this.register(path.concat(moduleName), module); 递归注册前会把module 的名放在 path的位
      // path.splice(0, -1) 是最后一项，是需要被挂的模块
      let parent = path.splice(0, -1).reduce((memo, current) => {
        return memo.getChild(current);
      }, this.root);
      parent.addChild(path[path.length - 1], newModule)
    }

    // 用户在模块中传了modules属性
    if (rootModule.modules) {
      // 循环模块 module模块的定义 moduleName模块的名字
      forEachValue(rootModule.modules, (module, moduleName) => {
        this.register(path.concat(moduleName), module)
      })
    }

  }
}
```
```
function installModule(store, rootState, path, module) {
  // 这里我需要遍历当前模块上的 actions、mutation、getters 都把他定义在store的_actions, _mutations, _wrappedGetters 中
  
}
```
installModule 就需要循环对当前模块处理对应的actions、mutation、getters。为此可以对Module类增加方法，来让其内部自己处理。
```
import { forEachValue } from '../util';

class Module {

  constructor(newModule) {
    this._raw = newModule;
    this._children = {};
    this.state = newModule.state
  }

  getChild(key) {
    return this._children[key];
  }

  addChild(key, module) {
    this._children[key] = module
  }

  // 给模块继续扩展方法
  forEachMutation(fn) {
    if (this._raw.mutations) {
      forEachValue(this._raw.mutations, fn)
    }
  }

  forEachAction(fn) {
    if (this._raw.actions) {
      forEachValue(this._raw.actions, fn);
    }
  }

  forEachGetter(fn) {
    if (this._raw.getters) {
      forEachValue(this._raw.getters, fn);
    }
  }

  forEachChild(fn) {
    forEachValue(this._children, fn);
  }
}

export default Module;
```
```
function installModule(store, rootState, path, module) {
  // 这里我需要遍历当前模块上的 actions、mutation、getters 都把他定义在store的_actions, _mutations, _wrappedGetters 中

  // 处理mutation
  module.forEachMutation((mutation, key) => {
    store._mutations[key] = (store._mutations[key] || [])
    store._mutations[key].push((payload) => {
      mutation.call(store, module.state, payload)
    })
  })

  // 处理action
  module.forEachAction((action, key) => {
    store._actions[key] = (store._actions[key] || [])
    store._actions[key].push((payload) => {
      action.call(store, store, payload)
    })
  })

  // 处理getter
  module.forEachGetter((getter, key) => {
    store._wrappedGetters[key] = function() {
      return getter(module.state)
    }
  })

  // 处理children
  module.forEachChild((child, key) => {
    // 递归加载
    installModule(store, rootState, path.concat(key), child)
  })

}
```
此时，已经把每个模块的actions、mutation、getters都挂到了store上，接下来需要对state处理。
```
// 将所有的子模块的状态安装到父模块的状态上
// 需要注意的是vuex 可以动态的添加模块
if (path.length > 0) {
  let parent = path.slice(0, -1).reduce((memo, current) => {
    return memo[current]
  }, rootState)
  // 如果这个对象本身不是响应式的 那么Vue.set 就相当于  obj[属性 ]= 值
  Vue.set(parent, path[path.length - 1], module.state);
}
```
到此已经完成模块的安装，接下里是要把这些放到Vue实例上面

### 模块与实例的关联
```
constructor(options) {

  const state = options.state; //数据变化要更新视图 （vue的核心逻辑依赖收集）
  this._mutations = {};
  this._actions = {};
  this._wrappedGetters = {};

  // 1.模块收集
  this._modules = new ModuleCollection(options);

  // 2.安装模块 根模块的状态中 要将子模块通过模块名 定义在根模块上
  installModule(this, state, [], this._modules.root);

  // 3,将状态和getters 都定义在当前的vm上
  resetStoreVM(this, state);

}
function resetStoreVM(store, state) {
  const computed = {}; // 定义计算属性
  store.getters = {}; // 定义store中的getters
  forEachValue(store._wrappedGetters, (fn, key) => {
    computed[key] = () => {
      return fn();
    }
    Object.defineProperty(store.getters, key, {
      get: () => store._vm[key] // 去计算属性中取值
    });
  })
  store._vm = new Vue({
    data: {
      $$state: state
    },
    computed // 计算属性有缓存效果
  });
}
```
相对应的Store类做以下修改
```
export class Store {
  constructor(options) {

    const state = options.state; //数据变化要更新视图 （vue的核心逻辑依赖收集）
    this._mutations = {};
    this._actions = {};
    this._wrappedGetters = {};

    // 1.模块收集
    this._modules = new ModuleCollection(options);

    // 2.安装模块 根模块的状态中 要将子模块通过模块名 定义在根模块上
    installModule(this, state, [], this._modules.root);

    // 3,将状态和getters 都定义在当前的vm上
    resetStoreVM(this, state);

  }

  commit = (type, payload) => { //保证当前this 当前store实例
    this._mutations[type].forEach(mutation => mutation.call(this, payload))
  }

  dispatch = (type, payload) => {
    this._actions[type].forEach(action => action.call(this, payload))
  }

  get state() { // 属性访问器   new Store().state  Object.defineProperty({get()})
    return this._vm._data.$$state
  }

}
```
## 命名空间nameSpaced
默认情况下，模块内部的 action、mutation 和 getter 是注册在全局命名空间的——这样使得多个模块能够对同一 mutation 或 action 作出响应。

>如果希望你的模块具有更高的封装度和复用性，你可以通过添加 namespaced: true 的方式使其成为带命名空间的模块。当模块被注册后，它的所有 getter、action 及 mutation 都会自动根据模块注册的路径调整命名。

>平常写上面基本上都要加上 namespaced，防止命名冲突，方法重复多次执行。现在就算每个 modules 的方法命一样，也默认回加上这个方法别包围的所有父结点的 key，核心就是 path 变量，在安装模块的时候把path处理下：
```
// 我要给当前订阅的事件 增加一个命名空间
let namespace = store._modules.getNamespaced(path); // 返回前缀即可
store._modules就是模块收集好的模块，给它增加一个获取命名空间的方法。

给ModuleCollection类增加一个getNamespaced方法，其参数就是path。

// 获取命名空间, 返回一个字符串
getNamespaced(path) {
  let root = this.root; // 从根模块找起来
  return path.reduce((str, key) => { // [a,c]
    root = root.getChild(key); // 不停的去找当前的模块
    return str + (root.namespaced ? key + '/' : '')
  }, ''); // 参数就是一个字符串
}
```
当然Module类也需要增加一个属性访问器
```
get namespaced() {
  return !!this._raw.namespaced
}
```
接下来就是在处理mutation,action,getters的时候key的值加上namespace就可以了。
```
// 处理mutation
module.forEachMutation((mutation, key) => {
  store._mutations[namespace + key] = (store._mutations[namespace + key] || [])
  store._mutations[namespace + key].push((payload) => {
    mutation.call(store, module.state, payload)
  })
})

// 处理action
module.forEachAction((action, key) => {
  store._actions[namespace + key] = (store._actions[namespace + key] || [])
  store._actions[namespace + key].push((payload) => {
    action.call(store, store, payload)
  })
})

// 处理getter
module.forEachGetter((getter, key) => {
  store._wrappedGetters[namespace + key] = function() {
    return getter(module.state)
  }
})
```
namespaces 核心就是对数据格式的处理，来进行发布与订阅。

## 插件
>Vuex 的 store 接受 plugins 选项，这个选项暴露出每次 mutation 的钩子。Vuex 插件就是一个函数，它接收 store 作为唯一参数

使用的时候：
```
const store = new Vuex.Store({
  // ...
  plugins: [myPlugin]
})
```
在插件中不允许直接修改状态——类似于组件，只能通过提交 mutation 来触发变化

先来看下一个vuex本地持久化的一个插件
```
function persists() {
    return function(store) { // store是当前默认传递的
        let data = localStorage.getItem('VUEX:STATE');
        if (data) {
            store.replaceState(JSON.parse(data));
        }
        store.subscribe((mutation, state) => {
            localStorage.setItem('VUEX:STATE', JSON.stringify(state));
        })
    }
}
```
插件返回一个函数，函数的参数就是store。其中replaceState, subscribe是关键点，也是vuex其中的2个api,接下来实现一下这2个方法。
```
export class Store {
  constructor(options) {

    const state = options.state; //数据变化要更新视图 （vue的核心逻辑依赖收集）
    this._mutations = {};
    this._actions = {};
    this._wrappedGetters = {};

    // 1.模块收集
    this._modules = new ModuleCollection(options);

    // 2.安装模块 根模块的状态中 要将子模块通过模块名 定义在根模块上
    installModule(this, state, [], this._modules.root);

    // 3,将状态和getters 都定义在当前的vm上
    resetStoreVM(this, state);

    // 插件内部会依次执行
    options.plugins.forEach(plugin=>plugin(this));

  }

  commit = (type, payload) => { //保证当前this 当前store实例
    this._mutations[type].forEach(mutation => mutation.call(this, payload))
  }

  dispatch = (type, payload) => {
    this._actions[type].forEach(action => action.call(this, payload))
  }

  get state() { // 属性访问器   new Store().state  Object.defineProperty({get()})
    return this._vm._data.$$state
  }

}
```
options.plugins.forEach(plugin=>plugin(this))就是让所有插件依次执行,参数就是store.
```
this._subscribes = [];
// ...
subscribe(fn){
  this._subscribes.push(fn);
}
```
subscribe就介绍一个函数，放入到一个数组或者队列中去。
```
// 处理mutation
module.forEachMutation((mutation, key) => {
  store._mutations[namespace + key] = (store._mutations[namespace + key] || [])
  store._mutations[namespace + key].push((payload) => {
    mutation.call(store, module.state, payload)
    store._subscribes.forEach(fn => {
      fn(mutation, rootState)
    })
  })
})
```
相应的在安装模块处理mutation的时候，需要让订阅的store._subscribes执行。fn的参数就是mutation和根状态。
```
replaceState(state){
  // 替换掉最新的状态
  this._vm._data.$$state = state
}
```
这是最简单的改变状态的方法，但此时虽然是ok的，但是mutation提交的还是旧值，mutation.call(store, module.state, payload)这个地方还是有点问题，module.state拿到的不是最新的状态。
```
function getState(store, path) { // 获取最新的状态 可以保证视图更新
  return path.reduce((newState, current) => {
    return newState[current];
  }, store.state);
}
```
可以通过这个方法能获取到最新的转态，相应的在处理mutation,getters的地方做相应调整。
```
// 处理mutation
module.forEachMutation((mutation, key) => {
  store._mutations[namespace + key] = (store._mutations[namespace + key] || [])
  store._mutations[namespace + key].push((payload) => {
    mutation.call(store, getState(store, path), payload)
    store._subscribes.forEach(fn => {
      fn(mutation, store.state)
    })
  })
})

// 处理getter
module.forEachGetter((getter, key) => {
  store._wrappedGetters[namespace + key] = function() {
    return getter(getState(store, path))
  }
})
```
之前的mutation.state全部替换成getState去获取最新的值。n(mutation, rootState) 也替换为fn(mutation, store.state)，这样就可以了。当然源码中并没有getState去或获取最新状态的方法。

## Vuex中的辅助方法
所谓辅助函数，就是辅助我们平时使用，说白了就是让我们偷懒。

我们在页面组件中可能会这样使用
```
<template>
  <div id="app">
    我的年龄是:{{this.$store.getters.age}}
    <button @click="$store.commit('changeAge',5)">同步更新age</button>
    <button @click="$store.commit('b/changeAge',10)">异步更新age</button>
  </div>
</template>
<script>

export default {
  computed: {
  },
  mounted() {
    console.log(this.$store);
  },
};
</script>
```
this.$store.getters.age这样用当然是可以，但是就是有点啰嗦，我们可以做以下精简
```
computed:{
    age() {
    return this.$store.getters.age
  }
}
```
this.$store.getters.age 直接替换成 age，效果肯定是一样的。但是写了在computed中写了age方法，感觉还是啰嗦麻烦，那再来简化一下吧,先看下用法：
```
computed:{
    ...mapState(['age'])
}
```
mapState实现
```
export function mapState(stateArr) {
    let obj = {};
    for (let i = 0; i < stateArr.length; i++) {
        let stateName = stateArr[i];
        obj[stateName] = function() {
            return this.$store.state[stateName]
        }
    }
    return obj
}
```
那如法炮制，mapGetters
```
export function mapGetters(gettersArr) {
    let obj = {};
    for (let i = 0; i < gettersArr.length; i++) {
        let gettName = gettersArr[i];
        obj[gettName] = function() {
            return this.$store.getters[gettName]
        }
    }
    return obj
}
```
mapMutations
```
export function mapMutations(obj) {
  let res = {};
  Object.entries(obj).forEach(([key, value]) => {
      res[key] = function (...args) {
          this.$store.commit(value, ...args)
      }
  })
  return res;
}
```
mapActions
```
export function mapActions(obj) {
  let res = {};
  Object.entries(obj).forEach(([key, value]) => {
      res[key] = function (...args) {
          this.$store.dispatch(value, ...args)
      }
  })
  return res;
}
```
其中这些方法都是在一个helpers文件中。在vuex/index文件中将其导入。
```
import { Store, install } from './store';

// 这个文件是入口文件，核心就是导出所有写好的方法
export default {
  Store,
  install
}

export * from './helpers';
```
## createNamespacedHelpers
>可以通过使用 createNamespacedHelpers 创建基于某个命名空间辅助函数。它返回一个对象，对象里有新的绑定在给定命名空间值上的组件绑定辅助函数。
```
export const createNamespacedHelpers = (namespace) => ({
  mapState: mapState.bind(null, namespace),
  mapGetters: mapGetters.bind(null, namespace),
  mapMutations: mapMutations.bind(null, namespace),
  mapActions: mapActions.bind(null, namespace)
})
```
## 总结
vuex的核心功能基本是完成，也能实现基本功能，不过看源码对很多细节做了处理，边界做了判断。而且其中用到 了很多设计模式以及很多技巧和算法。

通过自己实现一遍vuex,可以加深对vuex的理解和使用。

参考资料
作者：yutao618
链接：https://www.jianshu.com/p/df17e05abf94
来源：简书