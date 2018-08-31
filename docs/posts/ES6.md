# Map & Set
## Map
### 描述

保存键值对

以插入顺序迭代其元素，for...of 循环每次迭代返回一个 [key, value] 数组

使用 "SameValueZero" 比较键是否相等（包括 NaN ，正负 0）

### 与对象的区别

- 对象的键只能是字符串或者 Symbols ，但是 Map 的键可以是任意值（包括对象和函数）
- Map 可以通过 size 属性直接获取键值对个数
- Map 可迭代，对象需要先获取它的键数组然后再迭代
- 性能优势

## Set
### 描述

允许存储任何类型的唯一值

## WeakMap
### 描述

保存键值对，其中的键是弱引用的，必须为对象

### 与 Map 的区别

- 只能存放对象
- 不可枚举，没有 forEach，没有 size （因为弱引用的关系，无法得到确定的结果）

### 用法

与 Dom 关联，并在不需要 Dom 的时候能够立即被销毁防止内存泄漏

在构造函数里实现私有变量（保存 this 为键，通过 this 获取值），同时不用担心内存泄漏的问题

## WeakSet
### 描述

允许将弱保持对象存储在一个集合中，并且每个对象只能出现一次

# 异步

Javascript 本身并不是异步的，而 Javascript 程序是异步的，由其运行时环境提供，通过 event loop 实现

当遇到 IO 调用（或者定时器），就把它丢给运行时环境处理，自身（stack）继续执行后面的代码，当 IO 调用有了结果，会将结果及回调放在一个队列（callback queue）里，Javascript 线程会在合适的时机（执行栈为空）将回调函数取出（从头部）并执行

## Promise

```js
var p = new Promise(resolve => resolve(1)) // p -> Promise { <resolved>: 1 }
var p = new Promise(resolve => 1) // p -> Promise { <pending> }

const sleep = (val, time) => new Promise(resolve => setTimeout(() => resolve(val), time))
```

### Promise.prototype.then

- 如果then有多步骤的操作，那么前面步骤return的值会被当做参数传递给后面步骤的函数
- 如果参数没有处理当前状态的回调函数，那么返回的新 Promise 对象的状态和原来的一致
- onFulfilled：当状态是 fulfillment 时调用，如果不是函数的话会在内部被替换为 `(x) => x`（即原样返回 Promise）
- onRejected：当状态是 rejection 时调用
- 返回值：
  - 如果是一个值或者接受状态的 Promise ，那么会返回一个接受状态的 Promise ，并将这个值作为接受状态的回调函数的参数值
  - 如果是一个抛出的错误或者拒绝状态的 Promise ，那么会返回一个拒绝状态的 Promise ，并将这个值作为拒绝状态的回调函数的参数值
  - 如果返回一个 pending 状态的 Promise ，那么返回一个 pending 的 Promise ，并且它的终态与那个Promise的终态相同；同时，它变为终态时调用的回调函数参数与那个Promise变为终态时的回调函数的参数是相同的
  - 如果没有返回值的话会返回一个值为 undefined 并且 resolved 的新 Promise

  ```js
  // example
  Promise.resolve("foo")
    // 1. 接收 "foo" 并与 "bar" 拼接，并将其结果做为下一个resolve返回。
    .then(function(string) {
      return new Promise(function(resolve, reject) {
        setTimeout(function() {
          string += 'bar';
          resolve(string);
        }, 1);
      });
    })
    // 2. 接收 "foobar", 放入一个异步函数中处理该字符串
    // 并将其打印到控制台中, 但是不将处理后的字符串返回到下一个。
    .then(function(string) {
      setTimeout(function() {
        string += 'baz';
        console.log(string);
      }, 1)
      return string;
    })
    // 3. 打印本节中代码将如何运行的帮助消息，
    // 字符串实际上是由上一个回调函数之前的那块异步代码处理的。
    .then(function(string) {
      console.log("Last Then:  oops... didn't bother to instantiate and return " +
                  "a promise in the prior then so the sequence may be a bit " +
                  "surprising");

      // 注意 `string` 这时不会存在 'baz'。
      // 因为这是发生在我们通过setTimeout模拟的异步函数中。
      console.log(string);
  });
  ```

### Promise.prototype.catch

- `p.catch(onRejected)`; `p.catch(function(reason) {})`
- onRejected：Promise 被 rejected 时调用的一个函数，拥有一个参数 reason
- 实际上 catch(onRejected) 会在内部 calls then(undefined, onRejected)
- 如果 onRejected 抛出一个错误或返回一个本身失败的 Promise ，通过 catch() 返回的 Promise 的状态会是 rejected ，否则会是 resolved
- 对于Promise中的异常处理，我们建议用catch方法，而不是then的第二个参数

### 规范

- 状态：pending、fulfilled、rejected，不可逆
- Promise 必须实现 then 方法，且返回的也是 Promise（但是不是 return this，而是返回一个新的 Promise 再根据 onResolved 或 onRejected 函数来决定这个 Promise 的状态）

## Generator
### function*

- 需要使用 function* 定义，生成一个 Iterator 对象
- 生成的 Iterator 对象不会立即执行，需要用 next() 调用，但是执行完一个 yield 之后又会暂停，并返回  yield 表达式里的结果（到 value 中），最终遇到 return 结束，done 变为 true

### Generator.prototype.next()

- next 中的参数是传给指向 '上一个已经执行完了的 yield 语句' 的变量
  - 这个 yield 语句对应的值就是 next 中的参数，比如 let a = yield 2，a 的值由传参决定，并且是在 yield 2 的下一个 next 中
  - 这样就可以将 yield 中的 Promise 执行完后，获取值，作为下一次的 next 参数传入，等号左边就是 Promise 的结果
- yield*：后面接一个 Generator

### co

```js
// 简单实现 generator -> async await
const wrapAsync = (generatorFn) => {
  return (...args) => {
    const g = generatorFn.apply(null, args)
    return new Promise(resolve => {
      next()
      function next (data) {
        let res = g.next(data)
        if (res.done) resolve(res.value)
        else res.value.then(next)
      }
    })
  }
}
```

## async await
### async

- 使用 async function 代替 function*
- 执行 async function 之后返回的也是 Promise ，并且 return 的值可以被之后的 .then() 接收到（具体返回的处理应该和 Promise.prototype.then 差不多）

```js
// example
function resolveAfter2Seconds(x) {
  return new Promise(resolve => {
    setTimeout(() => {
      resolve(x);
    }, 2000);
  });
}

async function add1(x) { 
  var a = await resolveAfter2Seconds(20); 
  var b = await resolveAfter2Seconds(30); 
  return x + a + b; 
}
 
add1(10).then(v => { 
  console.log(v); // prints 60 after 4 seconds. 
});

async function add2(x) {
  var a = resolveAfter2Seconds(20);
  var b = resolveAfter2Seconds(30);
  return x + await a + await b;
}

add2(10).then(v => {
  console.log(v);  // prints 60 after 2 seconds.
});
```

### await

- 使用 await 代替 yield，后面必须跟 Promise 对象（异步）、其他数据类型（同步）
  - 由于 await 后面是 Promise 即可，所以跟一个 async function 也可以
- 用于使 async 函数暂停执行，等待表达式中的 Promise 解析完成后（如果不是 Promise 的话就同步执行完后）继续执行 async 函数并返回解决结果

```js 
// async 可加可不加
const pause = async time => new Promise(resolve => setTimeout(resolve, time))
```

## 工具

- thunkify：将异步函数需要的其他参数封装，返回一个只需要传入 callback 的异步函数
- co
- Q