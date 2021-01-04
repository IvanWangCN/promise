### 4.1 async 函数

1. 函数的返回值为 promise 对象
2. promise 对象的结果由 async 函数执行的返回值决定

```js
async function fn1() {
  //return 1
  // 返回一个Promise对象（PromiseStatus为resolved，PromiseValue为1）
  throw 2
  // 返回一个Promise对象（PromiseStatus为rejected，PromiseValue为2）
}
const result = fn1()
console.log(result)
```

这时，可以使用 result.then()：

```js
async function fn1() {
  //return 1
  throw 2
}
const result = fn1()
result.then(
  value => {
    console.log('onResolved()', value)
  },
  reason => {
    console.log('onRejected()', reason)
  },
)
// onRejected() 2
```

也可以在异步函数中返回一个 promise

```js
async function fn1() {
  //return Promise.reject(3)
  return Promise.resolve(3)
}
const result = fn1()
result.then(
  value => {
    console.log('onResolved()', value)
  },
  reason => {
    console.log('onRejected()', reason)
  },
)
// onResolved() 3
```

也就是说，一旦在函数前加 async，它返回的值都将被包裹在 Promise 中，这个 Promise 的结果由函数执行的结果决定。

上面的栗子都是立即成功/失败的 promise，也可以返回延迟成功/失败的 promise：

```js
async function fn1() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve(4)
    }, 1000)
  })
}
const result = fn1()
result.then(
  value => { // 过1s后才异步执行回调函数 onResolved()
    console.log('onResolved()', value)
  },
  reason => {
    console.log('onRejected()', reason)
  },
)
// onResolved() 4
```

### 4.2 await 表达式

#### MDN

[async](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/async_function)

[await](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/await)

#### 语法

[return_value] = await expression;

**表达式**

 一个 `Promise` 对象或者任何要**等待**的`值`。

**返回值**

 返回 Promise 对象的处理结果。如果**等待**的不是 Promise 对象，则返回该值本身。

**解释**

**await 表达式会暂停当前 async function 的执行，等待 Promise 处理完成。**

1. await 右侧的表达式一般为 promise 对象，但也可以是其他的值
2. 如果表达式是 promise 对象，await 返回的是 promise 成功的值
3. 如果表达式是其他值，直接将此值作为 await 的返回值

注意：

> **await 必须写在 async 函数中，但 async 函数中可以没有 await**

如果 await 的 promise 失败了，就会抛出异常，需要通过 try...catch 来捕获处理

```js
function fn2() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve(5)
    }, 1000)
  })
}

function fn4() {
  return 6
}

async function fn3() {
  //const p = fn2() // 这种写法只能得到一个promise对象
  const value = await fn2() // value 5
  //const value = await fn4() // value 6
  console.log('value', value)
}
fn3()
```

不写 await，只能得到一个 promise 对象。在表达式前面加上 await，1s后将得到 promise 的结果5，但是要用 await 必须在函数上声明 async。

await 右侧表达式 fn2() 为 promise，得到的结果就是 promise 成功的 value；await 右侧表达式 fn4() 不是 promise，得到的结果就是这个值本身。

Promise 对象的结果也有可能失败：

```js
function fn2() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      reject(5)
    }, 1000)
  }) 
}

async function fn3() {
  const value = await fn2()
  console.log('value', value)
}
fn3()
// 报错：Uncaught (in promise) 5
```

> **await 只能得到成功的结果，要想得到失败的结果就要用try/catch：**

```js
function fn2() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      reject(5)
    }, 1000)
  })
}

async function fn3() {
  try {
    const value = await fn2()
     console.log('value', value)
  } catch (error) {
    console.log('得到失败的结果', error)
  }
}
fn3()
// 得到失败的结果 5
```

下面这个栗子中，fn1 是第 2 种情况，fn2 是第 3 种情况，fn3 也是第 3 种情况

```js
async function fn1() { //async声明的异步回调函数将返回一个promise
  return 1
}
function fn2() {
  return 2
}
function fn3() {
  throw 3 // 抛出异常
}
async function fn4() {
  try {
    const value = await fn1() // value 1
    //const value = await fn2() // value 2
    //const value = await fn3() // 得到失败的结果 3
    console.log('value', value)
  } catch (error) {
    console.log('得到失败的结果', error)
  }
}
fn4()
```

