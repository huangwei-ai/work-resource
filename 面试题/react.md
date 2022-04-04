## 1、状态state和props的区别
state:是一种数据结构，用于组件挂载时所需数据的默认值，可能会随着时间的推移发生突变，*多数时候是作为用户事件行为的结果*
props:(properties)组件的配置，props由父组件传递给子组件，就*子组件而言props是不可变的*（immutable）
注：组件不能改变自身的props，但可以将其子组件的props放在一起进行统一管理，*props也不仅仅是数据——回调函数也可以通过props传递*

## 2、应该在Rect组件的何处发起Ajax请求？
在React组件中，应该在componentDidMount中发起网络请求。这个方法会在组件第一次挂载（被添加到Dom）时执行，在组件生命周期中仅执行一次
（在componentDidMount中法起网络请求可以保证这一个组件可以更新）
注：因为不能保证组件挂载之前Ajax就已经完成，在未挂载的组件上调用setSate，不起作用

## 3、React中三种构建组件的方式
React.createClass()、ES6 class和无状态函数(有可能尽量使用)
- 1、无状态函数式组件
只负责创建纯展示组件，只有传入的props，不涉及state操作
```
function HelloComponent(props, /* context */) {
  return <div>Hello {props.name}</div>
}
ReactDOM.render(<HelloComponent name="Sebastian" />, mountNode) 

```
特点：
- 组件不会被实例化，整体渲染性能得到提升
因为组件被精简成一个render方法的函数来实现的，由于是无状态组件，所以无状态组件就不会在有组件实例化的过程，无实例化过程也就不需要分配多余的内存，从而性能得到一定的提升。
- 组件不能访问this对象
无状态组件由于没有实例化过程，所以无法访问组件this中的对象，例如：this.ref、this.state等均不能访问。若想访问就不能使用这种形式来创建组件
- 组件无法访问生命周期的方法
因为无状态组件是不需要组件生命周期管理和状态管理，所以底层实现这种形式的组件时是不会实现组件的生命周期方法。所以无状态组件是不能参与组件的各个生命周期管理的。
- 无状态组件只能访问输入的props，同样的props会得到同样的渲染结果，不会有副作用

- 2、React.Component
```
class InputControlES6 extends React.Component {
    constructor(props) {
        super(props);

        // 设置 initial state
        this.state = {
            text: props.initialValue || 'placeholder'
        };

        // ES6 类中函数必须手动绑定
        this.handleChange = this.handleChange.bind(this);
    }

    handleChange(event) {
        this.setState({
            text: event.target.value
        });
    }

    render() {
        return (
            <div>
                Type something:
                <input onChange={this.handleChange}
               value={this.state.text} />
            </div>
        );
    }
}
InputControlES6.propTypes = {
    initialValue: React.PropTypes.string
};
InputControlES6.defaultProps = {
    initialValue: ''
};
```
- 3、React.createClass
```
var InputControlES5 = React.createClass({
    propTypes: {//定义传入props中的属性各种类型
        initialValue: React.PropTypes.string
    },
    defaultProps: { //组件默认的props对象
        initialValue: ''
    },
    // 设置 initial state
    getInitialState: function() {//组件相关的状态对象
        return {
            text: this.props.initialValue || 'placeholder'
        };
    },
    handleChange: function(event) {
        this.setState({ //this represents react component instance
            text: event.target.value
        });
    },
    render: function() {
        return (
            <div>
                Type something:
                <input onChange={this.handleChange} value={this.state.text} />
            </div>
        );
    }
});
InputControlES6.propTypes = {
    initialValue: React.PropTypes.string
};
InputControlES6.defaultProps = {
    initialValue: ''
};
//缺点
//React.createClass会自绑定函数方法（不像React.Component只绑定需要关心的函数）导致不必要的性能开销，增加代码过时的可能性。
//React.createClass的mixins不够自然、直观；
```

## 4、React中refs的作用是什么
Refs是React提供给我们*安全访问DOM元素或者1某个组件实例的句柄*
为元素添加ref属性——>然后再回调函数中接收该元素在DOM树中的句柄——>该值会作为回调函数的第一个参数返回
```
//类组件
class Custom extends Component{
  handleSubmit=()=>{
    console.log(this.input.value)
  }
  render(){
    return(
      <form onSubmit={this.handleSubmit}>
        <input type='text' ref={input=>(this.input=input)}/>
        <button type='submit'>Submit</button>
      </form>
    )
  }
}
//函数式组件
function CUstomFrom({handleSubmit}){
  let inputElement;
  return (
    <form onSubmit{()=>handleSubmit(inputElement.value)}>
      <input type='text' ref={input=>(inputElement=input)}/>
      <button type='submit'>Submit</button>
    </form>
  )
}
```


## 5、描述一下React diff原理
把树形结构按照层级分解，只比较同级元素
给列表每一个单元格添加唯一的Key属性，方便比较。
react只匹配相同的组件component合并操作，调用component的setState方法的时候，React将其标记为Dirty，到每一个事件循环结束，React检查所有标记dirty的component重新绘制，选择性子树渲染，开发人员可以重写shouldComponentUpdate提高diff性能

## 6、React的优势
- React速度很快：不直接对DOM进行操作，引入虚拟DOM的概念，安插在js逻辑和实际DOM之间，性能好。
- 跨浏览器兼容：虚拟DOM帮助我们解决跨浏览器问题，提供了标准化的API，甚至在IE8中都是没问题的。
- 一切都是Component：代码更加模块化，重用代码更容易，可维护性高。
- 单项数据流：Flux是一个用于在js应用中创建单项数据层的架构，它随着React视图库开发而被facebook概念化。
- 同构、纯粹得js：因为搜索引擎的爬虫程序依赖的是服务端响应而不是js的执行，预渲染应用有助于搜索引擎优化。
- 兼容性好：比如使用RequireJS来加载和打包，而Browserify和webpack适用于构建大型应用。

## 7、React的生命周期函数
初始化阶段
getDefaultProps:获取实例的默认属性
getInitialState：获取每个实例的初始化状态
componentWillMount：组件即将被装在，渲染到页面上
render：组件在这里生成虚拟的DOM节点
componentDidMount:组件真正被装在后，运行中状态
componentWillReceiveProps：组件将要接收到属性的时候调用
shouldComponentUpdate：组件接收到新属性或者新状态的时候（可以返回false，接收数据后不更新，组织render调用，后面的函数不会被继续执行了）
componentWillUpdate：组件即将更新，不能修改属性和状态
render：组件重新描绘
componentDidUpdate：组件已经更新
销毁阶段
componentWillUnmount：组件即将销毁


## 8、React怎么做事件代理
用document
```
class Index extend React.Component{
  render(){
    return{
    <div>
      <div id="parent">
        <div id="child" onClick={this.clickChild}></div>
      </div>
    </div>
    }
  }
  clickChild(e){
    //点击子元素
  }
  componentDidMount(){
    document.getElementByID('child').addEventListener('click',function(){
      //特殊的点击子元素
    })
  }
}
```
> #child元素绑定了两个点击事件，一个是通过React绑定的（代理绑定到document上），一个是通过addEventListener绑定的（真的绑定到#child元素上的）

如何阻止child的时间冒泡：
e.stopPropagation:阻止React模拟的事件冒泡
e.nativeEvent.stopPropagation:原生事件对象用域阻止DOM时间进一步捕获或者冒泡
e.nativeEvent.stopImmediatePropagation：原生事件对象用域阻止DOM时间进一步捕获或者冒泡，且该元素后续绑定的相同事件也一并组织


## 9、this的各种情况
- call apply bind指的this是谁就是谁（bind不会调用，只会将当前的函数返回）
fun.call(obj,a,b)
fun.apply(obj,[])
fun.bind(obj,a,b)()
- this的情况
1、以函数形式调用，this永远是window
2、以方法的行是调用，this是调用方法的对象
3、以构造函数的形式调用，this是创建的那个对象
4、使用call和apply调用，this是指定的那个对象
5、箭头函数：箭头函数的this看外层是否有函数（有，this就是外层函数的this，没有，就是window
6、特殊情况：通常意义上this指针只想最后调用它的对象，如果返回值是一个对象，那么this指向的就是那个返回的对象，如果不止一个对象，那么this还是指向函数的实例。

## 10、介绍promise，异常捕获，如何进行异常处理？
promise是解决回调地狱最好的工具，逼其直接使用回调函数，promise的语法结构更加清晰，代码可读性大大增强。
使用promise常遇到的问题
- 老旧浏览器没有promise全局对象怎么办
答:可以使用es6-promise-polyfill（标签引入、import引入），引入这个方法之后，会在window对象中加入Promise对象，就可以使用了
- 如何进行异常处理
答：promise一般再reject中处理错误，如果想要再catch中获取到，需要做一些操作？

## 11、常见的react优化
1、减少渲染的节点、降低渲染计算量（复杂度）
- 不要再渲染函数中进行不必要的计算（不要在渲染函数render中进行数组排序、数据转换、订阅事件、创建事件处理器等）
- 减少不必要的嵌套（不滥用高价组件/renderProps，优先使用props，React Hooks）
- 虚拟列表（长列表+复杂组件树，减少渲染的节点。例如：无限滚动列表，grid，表格，下拉列表，轮播图等）
- 惰性渲染（需要的时候采取渲染，例如：属性选择器、模拟弹窗，下拉列表等）
- 选择合适的样式方案（样式运行时性能：css>css-class>css-style
2、避免重新渲染(保证组件的纯粹性)
- 简化props（坚持单一原则，复杂会导致难以维护，会让组件边得难以预测和调试，提高缓存的命中率）
- 不变的事件处理器（避免使用箭头函数、使用hooks，使用useCallBack来包装事件处理器）
- 不可变数据（浅比较更高效）
- 简化state（组件需要相应变动，需要渲染到视图才放到state中，避免面不必要的重新渲染）
- 使用recompose精细化对比
3、精细化渲染
- 响应式数据的精细化渲染
- 不要滥用content

## 12、react的redux的使用
- store：保存数据的地方，一个应用只有一个store
- state：包含所有的数据
- action：view通知action改变state，从而改变view（修改state唯一的方法）

## 13、React的组件间通信
- 父传子
props
- 子传父
props+回调（props为作用域为父组件自身的函数，子组件调用该函数，传递的信息作为参数，传递到父组件作用域中）
- 兄弟组件
找到两个兄弟节点的父节点，结合上面两种方式由父节点转发消息进行通信
- 跨层级通信
context（主要是为了共享哪些对于一个组件树而言是全局的数据。例如：当前认证的用户，主题，语言）
- 发布订阅模式
发布者发布事件，订阅者监听事件并作出反应，可以引入event模块进行通信
- 全局状态管理工具
借助redux或者mobx等全局状态管理工具

## 14、redux中如何进行异步操作
异步中间件：redux-thunk，redux-saga，redux-observable

## 15、redux的优点
- 结果的可预测性——存在一个真实来源，即store
- 可维护性——代码更容易维护
- 服务器端渲染——服务器上创建的store传到客户端即可，对初始渲染非常有用
- 开发人员工具——操作到状态更改，可以实时跟踪
- 社区和生态系统——redux有一个巨大的社区
- 易于测试——小巧纯粹独立，简单一致


## 16、redux的组成
- Action——描述发生了什么事请的对象
- reducer——确定状态该如何变化的地方
- store——整个程序的状态/对象树保存的地方
- view——只显示store提供的数据

## 17、有关构造函数
1、在构造函数中调用super（props）的目的是什么
- 在super()被调用前，子类不能使用this
- props传递给super()便于在子类中能在constructor访问this.props
- 了在构造函数绑定this，还可以使用create-react-app

## 18、vue和react的区别
1、监听数据变化的实现原理不同
- vue通过getter/setter以及一些函数的劫持，能精确知道数据变化
- React默认是通过比较引用的方式（diff）进行，不优化可能导致大量不必要的VDOM的重新渲染（为什么不精确监听：设计理念上的区别，vue使用的是可变数据，React强调的是数据的不可变

2、数据流的不同
- vue1.0：Parent<——_props_——>Child<——_v-model_——>DOM(父子之间双向绑定、组件和DOM之间双向绑定)
- vue2.0:Parent——_props_——>Child<——_v-model_——>DOM（父子之间不再双向绑定）
- React:Parent<——_props_——>Child——_state_——>DOM（提倡单向数据流）

3、HoC和mixins
- vue组合不同功能的方式是通过mixins
- react组合不同功能是通过HOC

4、组件通信的区别
- vue三种方式事件组件通信：父——>子：props、子——>父：事件（$on,$emit)、多层级：provide/inject发布订阅模式
- react三种：父——>子：props、子——>父：事件和回调函数、多层级：context、provide/inject发布订阅模式

5、模板渲染方式不同（模板原理不同）
- vue：通过指令实现，如v-if，会把html弄得很乱
- react：在js代码中实现模板的常见语法，例如：插值、条件、循环
注：react的好处：react的render函数支持闭包，import的组件可以直接调用，但是vue就必须挂在this上进行一次中转，引入的组件必须在component中再声明一遍。

6、渲染过程不同
- vue：可以很快的计算出virtualDOM的差异，这是由于在渲染过程中，会跟踪每一个组件的依赖关系，不需要重新渲染整个组件树
- react：在应用状态被改变时，全部的子组件都会重新渲染，通过shouldComponentUpdate这个生命周期可以进行控制
注：如果应用交互复杂，需要处理大量的UI变化，使用VirtualDOM是一个很好的选择，但是如果更新不频繁，性能不如直接操纵DOM

7、框架本质不同
- vue：本质是MVVM框架由MVC发展而来
- react：前端组件化框架，由后端组件化发展而来

8、vuex和redux的区别
- vuex：
    $store被直接注入到组件实例，可以灵活使用
    使用dispatch、commit提交更新
    使用mapState或者直接通过this.$store读取数据
    灵活：组件中既可以dispatch action，也可以commit updates
    数据是可变的，直接修改
    检测数据通过getter/setter
    构建简单，迅捷，灵活的小型项目
- redux：
    每一个组件都需要显式的用connect吧需要的props和dispatch链接起来
    redux在组件中只能进行dispatch，不能直接调用reducer进行修改
    redux使用的是不可变数据，用新得state替换旧的state
    检测数据时通过diff比较差异
    偏向于构建稳定大型应用


## 19、userEffect
useEffect可以看成是componentDidMount、componentDidUpdate和componentWillUnmount三者的结合
userEffect(callback,[source])接收两个参数调用方式如下
```
useEffect(()=>{
  //组件挂载后执行事件绑定
  console.log('mounted');
  addEventListener()
  //组件update时会执行事件解绑
  return ()=>{
    console.log('willUnmount')
    removeEventListener()
  }
},[source])
```
生命周期函数的调用主要通过第二个参数来控制（设置触发条件仅当source发生改变时才会触发）
- [source]参数不传：每次会优先调用上次保存的函数中返回的那个函数，然后再调用外部那个函数
- 传参传[]：外部的函数只会在初始化时调用一次返回那个函数，也只会在最终组件卸载的时候调用一次
- 参数有值：只会监听数组中的值发生变化后才优先返回那个函数，再调用哪个函数再调用外部的函数

