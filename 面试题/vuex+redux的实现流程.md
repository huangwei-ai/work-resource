## vuex的执行流程
```

//store/index.js:引入所有的vuex模块
import Vue from 'vue';
import Vuex from 'vuex';
import xxx from '@/views/xxx/modules';//引入某一个文件夹下的vuex文件
Vue.use(Vuex);
export default new Vuex.Store({
    state:{//变量声明
        userInfo:{}
    }
    mutations:{//写入非异步方法的缓存方法
      setUserInfo: (state: any, result: any) => {
          state.userInfo = result;
        },
    }
    actions:{//执行需要进行异步请求的方法
      getUserInfo: async ({ commit }) => {
            const response: any = await request.get('/nethos/organ/accounts/info');
            const { data } = response;
            const { data: result } = data;
            commit('setUserInfo', result);//调用mutation方法将值缓存到变量中
            return result;
          },
    }
    modules:{//这里可以将引入的vuex模块导入
      xxx,
    }
})


//子modules中的index.js
import xxxx from './xxxx';//引入子vuex:结构和总的vuex文件相同包含：state、mutations、actions
export default {//导出子vuex
  namespaced: true,//是否使用命名空间：可以用来让文件的请求位置只能访问固定的vuex文件
  modules: {
    xxxx,
  },
};


<!-- 使用 -->
//调用mutations中的方法
this.$store.dispatch("setUserInfo",data)
//调用actions中的方法
this.$store.dispatch("getUserInfo")
//获取存进去过的state中的数据
this.$store.state.userInfo
```

## redux的执行流程
```

//store/index.js文件：引入所有的redux模块

import { createStore, combineReducers, applyMiddleware } from 'minapp-rct/redux';
//createStore：对象用于创建仓库，仓库中存储这要用到的项目中的属性和方法
//combineReducers：把一个由多个不同 reducer 函数合并成一个最终的 reducer 函数
//applyMiddleware：让你包装 store 的 dispatch 方法来达到你想要的目的

import thunk from 'redux-thunk';
//redux-thunk：让 dispatch 中可以写函数,

import logger from 'redux-logger';
import { combineModules } from 'minapp-rct/reduxUtils';

import xxx from './xxx';//引入需要导入的模块

let modules = combineModules({//挂载某个导入的模块
  xxx,
})
let middleware=[thunk]

const Store = createStore(combineReducers(modules.reducers), applyMiddleware(...middleware));
// applyMiddleware(...middleware)===applyMiddleware(thunk)

export const actions = modules.actions;//将action导出，后面可以引入后直接调用

export default Store;//将整个store进行导出


-------------
<!-- store下面挂载的模块文件xxx.js -->

import { createModule } from 'minapp-rct/reduxUtils';
//state 是只读，Redux并没有暴露出直接修改state的接口，必须通过action来触发修改使用纯函数来修改state，reducer必须是纯函数
export default createModule({
    name:'xxx',//用于后面导出
    initialState:{
        userInfo:{},//属性声明并赋初始值
    }
    actions:{//必须是纯函数，只做赋值操作
        setUserInfo:(v)=>v
    }
    asyncActions:(actions)=>({//可以用来发送异步请求
        getUserInfo() {//异步获取用户信息的方法
          return async (dispatch, getState) => {
              let { data } = await IMAPI.getUserInfo();//进行请求的发送
              const userMap = getState().xxx.xxx对象信息;//可以用此方法获取已经缓存的name为xxx的redux中的xxx对象信息
              dispatch(actions.setUserInfo(userMap));//用actions中的纯函数赋值
      };
    })

    reducer:{//声明的纯函数在这里实现
        setUserInfo(state, action){//action.payload就是函数调用时传入的数据信息
            return { ...state, userInfo: action.payload };
      }, 
    }


---------------
<!-- 使用redux进行操作 -->
import Store from '../store/index';
import { actions } from '../store/index';
import { connect } from 'minapp-rct';//使用页面为页面
import { connectComponent } from 'minapp-rct';//使用页面为组件
const page={//页面时
//const component = {//组件时
 state(state) {//参数获取
    const { im } = state;//获取对应redux的导出对象名称
    return {
			userInfo: im.userInfo,//获取对应的redux的数据
    }
  },
  actions(dispatch, state) {
    return {
      setUserInfo(info) {//调用redux的reducer中的方法并存进去值
        dispatch(actions.im.setUserInfo(info));
      },
      getUserInfo() {//调用redux中的asyncAction中的方法发送异步请求获取数据
        dispatch(actions.im.getUserInfo());
      },
    };
  },
  //只引入Store时，通过名称调用redux中的reducer中的方法或者asyncAction中的方法
  Store.dispatch(actions.im.setUserInfo(userInfo));
}

Page(connect()(page))//为页面时
//Component(connectComponent()(component));//为组件时
```