
## 概念
- 函数防抖：触发高频率事件后n秒内函数只会执行一次，如果n秒内高频率事件再次被执行则重新计算时间
- 函数节流：高频率事件触发，但在n秒内只会执行一次，所以节流会稀释函数的执行频率。
- 函数节流（throttle）与 函数防抖（debounce）都是为了限制函数的执行频次，以优化函数触发频率过高导致的响应速度跟不上触发频率，出现延迟，假死或卡顿的现象。节省浏览器消耗内存，给用户良好的体验

## 应用场景
在进行窗口的resize、scroll，输入框内容校验等操作时，如果事件处理函数调用的频率无限制，会加重浏览器的负担，导致用户体验非常糟糕。此时我们可以采用debounce（防抖）和throttle（节流）的方式来减少调用频率，同时又不影响实际效果。

### 1、函数防抖(debounce)

- 实现方式：每次触发事件时设置一个延迟调用方法，并且取消之前的延时调用方法
- 缺点：如果事件在规定的时间间隔内被不断的触发，则调用方法会被不断的延迟

通过案例看看实现效果：

我们给一个div通过鼠标的移入触发事改变div的innerhtml值
```
<style>
        .container {
        width: 100%;
        height: 200px;
        background-color: rgb(166, 187, 184);
        text-align: center;
        line-height: 200px;
        background-size: 30px;
      }
    </style>
     <div class="container"></div>
    <script>
        let dv1 = document.querySelector(".container");
       
      let counter = 0;
      function doSome() {
        dv1.innerHTML = counter++;
      }
     dv1.addEventListener('mousemove', doSome)
     </script>
```

可以看出当鼠标在盒子内移动时会频繁触发事件，影响功能
通过函数防抖与节流可以解决这个问题
防抖：
```
let dv1 = document.querySelector(".container");
       
      let counter = 0;
      function doSome() {
        dv1.innerHTML = counter++;
      }
       //防抖功能函数，接受传参
                    function debounce(fn) {
                    // 创建一个标记用来存放定时器的返回值
                    let timeout = null;
                    return function() {
                        // 每次当用户操作的时候，把前一个定时器清除
                        clearTimeout(timeout);
                        // 然后创建一个新的 setTimeout，
                        // 这样就能保证操作后的 interval 间隔内
                        // 如果用户还操作了的话，就不会执行 fn 函数
                        timeout = setTimeout(() => {
                        fn.call(this, arguments);
                        }, 1000);
                    };
                    }
```

这里我鼠标移动了多次，但是在这移动的过程中数值没没有变化，当停下时秒后数值在变化。也就是从最后一次才开始计算。
- 函数防抖：就是将几次操作合并为一此操作进行。其原理是维护一个计时器，规定在delay时间后触发函数，但是在delay时间内再次触发的话， 就会取消之前的计时器而重新设置。这样一来，只有最后一次操作能被触发。

## 防抖应用：
- scroll滚动事件的触发；

- 网络请求一些应用；

- 表单验证

- 按钮提交事件

- 浏览器窗口缩放

- 搜索框输入

函数节流：
**思路：**每次触发事件时都判断当前是否有等待执行的延时函数
```
 let dv1 = document.querySelector(".container");
       
      let counter = 0;
      function doSome() {
        dv1.innerHTML = counter++;
      }
      function throttle(fn) {
        // 通过闭包保存一个标记
        let canRun = true;
        return function () {
          // 判断标志是否为 true，不为 true 则中断函数
          if (!canRun) {
            return;
          }
          // 将 canRun 设置为 false，防止执行之前再被执行
          canRun = false;
          // 定时器
          setTimeout(() => {
            fn.call(this, arguments);
            // 执行完事件（比如调用完接口）之后，重新将这个标志设置为 true
            canRun = true;
          }, 1000);
        };
      }
```

节流：使得一定时间内只触发一次函数。原理是通过判断是否到达一定时间来触发函数。

## 两者区别：
- 函数节流不管事件触发有多频繁，都会保证在规定时间内一定会执行一次真正的事件处理函数，
- 都是为了限制函数的执行频次，以优化函数触发频率过高导致的响应速度跟不上触发频率，出现延迟，假死或卡顿的现象。
而函数防抖只是在最后一次事件后才触发一次函数。
比如在页面的无限加载场景下，我们需要用户在滚动页面时，
每隔一段时间发一次 Ajax 请求，而不是在用户停下滚动页面操作时才去请求数据。这样的场景，就适合用节流技术来实现。

————————————————
参考资料
CSDN博主「Courage-lu」的原创文章
原文链接：https://blog.csdn.net/weixin_45846805/article/details/107300633