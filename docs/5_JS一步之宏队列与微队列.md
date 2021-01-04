![img](https://img-blog.csdnimg.cn/20200703144207979.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNTA4ODMy,size_16,color_FFFFFF,t_70)

1. JS 中用来**存储待执行回调函数的队列包含2个不同特定的队列**

2. 宏队列：用来保存待执行的宏任务（回调），比如：定时器回调/DOM 事件回调/ajax 回调

3. 微队列：用来保存待执行的微任务（回调），比如：promise 的回调/MutationObserver 的回调

4. JS 执行时会区别这2个队列

   (1) JS 引擎首先必须执行所有的初始化同步任务代码

   (2) 每次准备取出第一个宏任务前，都要将所有的微任务一个一个取出来执行

```js
setTimeout(() => { // 会立即被放入宏队列
  console.log('timeout callback1()')
}, 0)
setTimeout(() => { // 会立即被放入宏队列
  console.log('timeout callback2()')
}, 0)
Promise.resolve(1).then(
  value => { // 会立即被放入微队列
    console.log('Promise onResolved1()', value)
  }
)
Promise.resolve(1).then(
  value => { // 会立即被放入微队列
    console.log('Promise onResolved2()', value)
  }
)
// Promise onResolved1() 1
// Promise onResolved2() 1
// timeout callback1()
// timeout callback2()
```

> **先执行所有的同步代码，再执行队列代码。队列代码中，微队列中的回调函数优先执行。**

```js
setTimeout(() => { // 会立即被放入宏队列
  console.log('timeout callback1()')
  Promise.resolve(1).then(
  value => { // 会立即被放入微队列
    console.log('Promise onResolved3()', value)
  }
}, 0)
setTimeout(() => { // 会立即被放入宏队列
  console.log('timeout callback2()')
}, 0)
Promise.resolve(1).then(
  value => { // 会立即被放入微队列
    console.log('Promise onResolved1()', value)
  }
)
Promise.resolve(1).then(
  value => { // 会立即被放入微队列
    console.log('Promise onResolved2()', value)
  }
)
// Promise onResolved1() 1
// Promise onResolved2() 1
// timeout callback1()
  // Promise onResolved3() 1
// timeout callback2()
```

执行完 `timeout callback1()` 后 `Promise onResolved3()` 会立即被放入微队列。在执行 `timeout callback2()` 前，`Promise onResolved3()` 已经在微队列中了，所以先执行 `Promise onResolved3()`。

