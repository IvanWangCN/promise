### 6.1 面试题1

```js
setTimeout(() => {
  console.log(1)
}, 0)
new Promise((resolve) => {
  console.log(2)
  resolve()
}).then(() => {
  console.log(3)
}).then(() => {
  console.log(4)
})
console.log(5)
// 2 5 3 4 1
/*
同步：[2,5]
异步：
宏队列：[1]
微队列：[3,4]
*/
```

**2 是 excutor 执行器，是同步回调函数，所以在同步代码中。.then() 中的函数才是异步回调**

其中，执行完 2 后改变状态为 resolve，第一个 .then() 中的 3 会放入微队列，但还没执行（promise 是 pending 状态），就不会把结果给第二个 then()，这时，4 就会缓存起来但不会被放入微队列。只有在微队列中的 3 执行完后才把 4 放入微队列。

所以顺序是：

1 放入宏队列，2 执行，3 放入微队列，4 缓存起来等待 Promise 的状态改变，5 执行，微队列中的 3 执行，4 放入微队列，微队列中的 4 执行，宏队列中的 1 执行。

### 6.2 面试题2

```js
const first = () => ( // 省略return所以不用{}而用()
  new Promise((resolve, reject) => {
    console.log(3)
    let p = new Promise((resolve, reject) => {
      console.log(7)
      setTimeout(() => {
        console.log(5)
        resolve(6) //没用，状态只能改变一次，在resolve(1)时就改变了
      }, 0)
      resolve(1)
    })
    resolve(2)
    p.then((arg) => {
      console.log(arg)
    })
  })
)
first().then((arg) => {
  console.log(arg)
})
console.log(4)
// 3 7 4 1 2 5
/*
宏：[5]
微：[1,2]
*/
```

### 6.3 面试题3

```js
setTimeout(() => {
  console.log("0")
}, 0)
new Promise((resolve, reject) => {
  console.log("1")
  resolve()
}).then(() => {
  console.log("2")
  new Promise((resolve, reject) => {
    console.log("3")
    resolve()
  }).then(() => {
    console.log("4")
  }).then(() => {
    console.log("5")
  })
}).then(() => {
  console.log("6")
})

new Promise((resolve, reject) => {
  console.log("7")
  resolve()
}).then(() => {
  console.log("8")
})
// 1 7 2 3 8 4 6 5 0
/*
宏：[0]
微：[2, 8, 4, 6, 5]
*/
```

顺序：

0 放入宏队列，同步执行 1，2 放入微队列，6 缓存到内部，同步执行 7，8 放入微队列，取出微队列中的 2 执行，同步执行 3，4 放入微队列，5 缓存到内部，6 放入微队列(因为 6 的前一个 promise 已经执行完了返回成功结果 undefined)，取出微队列中的 8 执行，取出微队列中的 4 执行，5 放入微队列，取出微队列中的 6 执行，取出微队列中的 5 执行，取出宏队列中的 0 执行