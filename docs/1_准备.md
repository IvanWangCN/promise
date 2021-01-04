### 1.1 区别实例对象与函数对象
实例对象：new 函数产生的对象，称为实例对象，简称为对象
函数对象：将函数作为对象使用时，称为函数对象
``` javascript
function Fn() { // Fn只能称为函数
}
const fn = new Fn() // Fn只有new过的才可以称为构造函数
//fn称为实例对象
console.log(Fn.prototype)// Fn作为对象使用时，才可以称为函数对象
Fn.bind({}) //Fn作为函数对象使用
$('#test') // $作为函数使用
$.get('/test') // $作为函数对象使用
```
> **()的左边必然是函数，点的左边必然是对象**

### 1.2 回调函数
**同步回调**
定义：立即执行，完全执行完了才结束，不会放入回调队列中
举例：数组遍历相关的回调 / Promise的excutor函数

``` javascript
const arr = [1, 3, 5];
arr.forEach(item => { // 遍历回调，同步回调，不会放入队列，一上来就要执行
  console.log(item);
})
console.log('forEach()之后')
// 1
// 3
// 5
// "forEach()之后"
```
**异步回调**
定义：不会立即执行，会放入回调队列中将来执行
举例：定时器回调 / ajax回调 / Promise成功或失败的回调

``` javascript
// 定时器回调
setTimeout(() => { // 异步回调，会放入队列中将来执行
  console.log('timeout callback()')
}, 0)
console.log('setTimeout()之后')
// “setTimeout()之后”
// “timeout callback()”
```
``` javascript
// Promise 成功或失败的回调
new Promise((resolve, reject) => {
  resolve(1)
}).then(
  value => {console.log('value', value)},
  reason => {console.log('reason', reason)}
)
console.log('----')
// ----
// value 1
```
> **js 引擎是先把初始化的同步代码都执行完成后，才执行回调队列中的代码**

### 1.3 JS 的 error 处理
**错误的类型**

Error：所有错误的父类型

ReferenceError：引用的变量不存在
``` javascript
console.log(a) // ReferenceError:a is not defined
```
TypeError：数据类型不正确

```javascript
let b
console.log(b.xxx)
// TypeError:Cannot read property 'xxx' of undefined
let b = {}
b.xxx()
// TypeError:b.xxx is not a function
```

RangeError：数据值不在其所允许的范围内

```javascript
function fn() {
  fn()
}
fn()
// RangeError:Maximum call stack size exceeded
```

SyntaxError：语法错误

```javascript
const c = """"
// SyntaxError:Unexpected string
```

**错误处理**

捕获错误：try ... catch

抛出错误：throw error

```javascript
function something() {
  if (Date.now()%2===1) {
    console.log('当前时间为奇数，可以执行任务')
  } else { //如果时间为偶数抛出异常，由调用来处理
    throw new Error('当前时间为偶数，无法执行任务')
  }
}

// 捕获处理异常
try {
  something()
} catch (error) {
  alert(error.message)
}
```

**错误对象**

massage 属性：错误相关信息

stack 属性：函数调用栈记录信息

```javascript
try {
  let d
  console.log(d.xxx)
} catch (error) {
  console.log(error.message)
  console.log(error.stack)
}
console.log('出错之后')
// Cannot read property 'xxx' of undefined
// TypeError:Cannot read property 'xxx' of undefined
// 出错之后
```

> **因为错误被捕获处理了，后面的代码才能运行下去，打印出‘出错之后’**