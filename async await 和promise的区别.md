现在流行的写请求方式就是使用Ascyn await 因为十分的简洁用！

## 什么是Async/Await?
　　
- async/await是写异步代码的新方式，以前的方法有回调函数和Promise。
- async/await是基于Promise实现的，它不能用于普通的回调函数。
- async/await与Promise一样，是非阻塞的。
- async/await使得异步代码看起来像同步代码，这正是它的魔力所在。

 ## Async/Await语法

　假设函数getJSON返回值是 Promise，并且 Promise resolves 有一些JSON 对象。我们只想调用它并且记录该JSON并且返回完成。

### 1）使用Promise：
```
    const makeRequest = () =>
        getJSON().then(data => {
            console.log(data)
            return "done"
        })

    makeRequest()
 ```
 

### 2）使用Async：
```
    const makeRequest = async () => {
        // await getJSON()表示console.log会等到getJSON的promise成功reosolve之后再执行。
        console.log(await getJSON)
        return "done"
    }

    makeRequest()

```
 

 ### 区别：
- 1）函数前面多了一个aync关键字。await关键字只能用在aync定义的函数内。async函数会隐式地返回一个promise，该promise的reosolve值就是函数return的值。(示例中reosolve值就是字符串”done”)
- 2）第1点暗示我们不能在最外层代码中使用await，因为不在async函数内。例如：

```
    // 不能在最外层代码中使用await
    await makeRequest()

    // 这是会出事情的 
    makeRequest().then((result) => {
        // 代码
    })
```
 ## 为什么Async/Await更好？

- 1）使用async函数可以让代码简洁很多，不需要像Promise一样需要些then，不需要写匿名函数处理Promise的resolve值，也不需要定义多余的data变量，还避免了嵌套代码。
- 2） 错误处理：Async/Await 让 try/catch 可以同时处理同步和异步错误。在下面的promise示例中，try/catch 不能处理 JSON.parse 的错误，因为它在Promise中。我们需要使用 .catch，这样错误处理代码非常冗余。并且，在我们的实际生产代码会更加复杂。

```
    const makeRequest = () => {
        try {
            getJSON().then(result => {
                // JSON.parse可能会出错
                const data = JSON.parse(result)
                console.log(data)
            })
            // 取消注释，处理异步代码的错误
            // .catch((err) => {
            //   console.log(err)
            // })
        } catch (err) {
            console.log(err)
        }
    }
```
使用aync/await的话，catch能处理JSON.parse错误:
```
    const makeRequest = async () => {
        try {
            // this parse may fail
            const data = JSON.parse(await getJSON())
            console.log(data)
        } catch (err) {
            console.log(err)
        }
    }
```
- 3）条件语句:条件语句也和错误捕获是一样的，在 Async 中也可以像平时一般使用条件语句
  <b>Promise：</b>
```
    const makeRequest = () => {
        return getJSON().then(data => {
            if (data.needsAnotherRequest) {
                return makeAnotherRequest(data).then(moreData => {
                    console.log(moreData)
                    return moreData
                })
            } else {
                console.log(data)
                return data
            }
        })
    }
```
<b> Async/Await：</b>

```
    const makeRequest = async () => {
        const data = await getJSON()
        if (data.needsAnotherRequest) {
            const moreData = await makeAnotherRequest(data);
            console.log(moreData)
            return moreData
        } else {
            console.log(data)
            return data
        }
    }
```

- 4）中间值:你很可能遇到过这样的场景，调用promise1，使用promise1返回的结果去调用promise2，然后使用两者的结果去调用promise3。

```
    const makeRequest = () => {
        return promise1().then(value1 => {
            return promise2(value1).then(value2 => {
                return promise3(value1, value2)
            })
        })
    }
```
　　　 如果 promise3 不需要 value1，嵌套将会变得简单。如果你忍受不了嵌套，你可以将value 1 & 2 放进Promise.all来避免深层嵌套，但是这种方法为了可读性牺牲了语义。除了避免嵌套，并没有其他理由将value1和value2放在一个数组中。

```
    const makeRequest = () => {
        return promise1().then(value1 => {
            return Promise.all([value1, promise2(value1)])
        }).then(([value1, value2]) => {
            return promise3(value1, value2)
        })
    }
```
使用async/await的话，代码会变得异常简单和直观。
```
    const makeRequest = async () => {
        const value1 = await promise1()
        const value2 = await promise2(value1)
        return promise3(value1, value2)
    }
```

- 5）错误栈:如果 Promise 连续调用，对于错误的处理是很麻烦的。你无法知道错误出在哪里。

```
    const makeRequest = () => {
        return callAPromise()
            .then(() => callAPromise())
            .then(() => callAPromise())
            .then(() => callAPromise())
            .then(() => callAPromise())
            .then(() => {
                throw new Error("oops");
            })
    }

    makeRequest().catch(err => {
        console.log(err);
        // output
        // Error: oops at callAPromise.then.then.then.then.then (index.js:8:13)
    })
```

async/await中的错误栈会指向错误所在的函数。在开发环境中，这一点优势并不大。但是，当你分析生产环境的错误日志时，它将非常有用。这时，知道错误发生在makeRequest比知道错误发生在then链中要好。

```
    const makeRequest = async () => {
        await callAPromise()
        await callAPromise()
        await callAPromise()
        await callAPromise()
        await callAPromise()
        throw new Error("oops");
    }

    makeRequest().catch(err => {
        console.log(err);
        // output
        // Error: oops at makeRequest (index.js:7:9)
    })
```

- 6）调试:async/await能够使得代码调试更简单。2个理由使得调试Promise变得非常痛苦:

《1》不能在返回表达式的箭头函数中设置断点
《2》如果你在.then代码块中设置断点，使用Step Over快捷键，调试器不会跳到下一个.then，因为它只会跳过异步代码。

使用await/async时，你不再需要那么多箭头函数，这样你就可以像调试同步代码一样跳过await语句。

## 总结
saync await 比promise：代码简洁、方便错误处理、减少代码冗余、常用语句如if-else使用更加方便、连续调用比较好找到错误、调试更加简单，避免了部分promise会遇到的问题