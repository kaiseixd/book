# 基础
## null/undefined

- null 代表没有值（此处不应该有值，占位符），undefined 代表不存在的值（缺少值，未定义）
- typeof、Number、Object.prototype.toString
- undefined 是一个预定义的全局变量（但是不能赋为其他值），而 null 是关键字
- JavaScript 使用 undefined 并且程序员应该使用 null
- void 0 永远返回 undefined

### 设计 undefined 的原因

一开始像 java 一样，只设置了 null 表示无值，是一个对象，并且预期可以转换为数字 0 。但是 Brendan 觉得'无'值最好不是对象，并且由于错误处理机制，最好能转换成 NaN ，而不是 0 ，所以设计了 undefined 。

### typeof undefined

typeof 一个并未声明的变量，不会报 ReferenceError，而是会返回 undefined

> 当然如果访问对象的未定义属性的话也是返回 undefined，比如 window

## NaN
### isNaN

只要不是数字的都返回 true（比如字符串、undefined、对象）

但是能转换为数字的非 number 也能返回 false（比如 '1'、null、[1]）

### Number.isNaN

polyfill：`typeof n === 'number' && window.isNaN(n)`

## Number

js 的数字采用 IEEE 754 标准来实现

### 属性

- toFixed：限定小数位数
- toPrecision：限定位数
- Number.EPSILON
- Number.MAX_VALUE
- Number.MAX_SAFE_INTEGER：2 ** 53 - 1
- Number.isInteger：（es6，polyfill：`typeof num === 'number' && num % 1 === 0`）

### 进制

- 16：0xf3
- 8：0363, 0o363, 0O363
- 2：0b101

> es6 的严格模式不再支持类似 0363 的八进制格式，需要加上 o  

### 正负0

- `+0 === -0 -> true`
- 使用 `1 / -0 === -Infinity` 或者 Object.is() 来区分两者
- -0 数字转换成字符串，结果是 '0'；'-0' 转换成数字，结果是 -0

## Object

所有 typeof 返回值为 'object' 的对象，都包含一个内部属性 [[Class]]，无法直接访问，只能通过 `Object.prototype.toString` 来查看

> 基础类型 null 和 undefined 内部有 [[Class]]，其他基础类型通过包装对象找到 [[Class]]

### 属性

- valueOf：获取对象的基本类型值

### native/host objects
#### 原生对象

与宿主无关，独立于宿主环境的 ECMAScript 实现提供的对象

其中部分 native objects 是内置的（built-in），还有一些会在 js 的执行过程中创建（Arguments）

#### 宿主对象

由宿主环境提供的对象，除上者之外都是

## Array

empty：删除数组中的项后会变成 empty，这个 empty slot 仍然会计入长度，而获取值的时候会返回 undefined

### 属性

- sort：V8 引擎的 sort 方法，在 10 以内是插入排序，10 以上（注释里写的是 22）是快速排序（由于快排不稳定，所以不太适合直接使用 sort 来排之前已经排序好的数据）
- `Array.apply( null, { length: 2 } )` -> `Array(undefined, undefined)` -> `[undefined, undefined]`

### 转换为数组

```js
Array.from(string)
[].concat(...rest)
```

### 类数组

拥有 length 属性，其它属性（索引）为非负整数（但是不具有数组所具有的方法，需要使用 Function.call 间接调用）

包括：arguments、DOM 查询获取的 DOM 元素列表

#### 判断

```js
function isArrayLike(o) {
	if (o &&                              // o is not null, undefined, etc.
		typeof o === 'object' &&            // o is an object
		isFinite(o.length) &&               // o.length is a finite number
		o.length >= 0 &&                    // o.length is non-negative
		o.length===Math.floor(o.length) &&  // o.length is an integer
		o.length < 4294967296)              // o.length < 2^32
		return true;                        // Then o is array-like
	else
		return false;                       // Otherwise it is not
}
```

#### 转化为数组

```js
// 由于 IE9 以前的版本使用 COM 来实现 DOM ，所以不支持这种方法
Array.prototype.slice.call(arrayLike)
Array.prototype.splice.call(arrayLike, 0)
Array.prototype.concat.apply([], arrayLike)
// es6 方法，从一个类数组或可迭代对象中创建一个新的数组实例
Array.from(arrayLike)
```

## tip
### document.write()

将一个文本字符串写入由 document.open() 打开的一个文档流

在关闭（已加载）的文档上调用 document.write 会自动调用 document.open，这将清空该文档的内容（并在写入完之后调用 document.close）

### async/defer

- async：加载完就执行；优先执行先加载完的脚本；
- defer：页面加载和解析完毕后执行；按脚本在页面中出现的顺序依次执行；

### JSON.stringify()

不安全的 JSON 值：undefined, function, symbol, 包含循环引用的对象（前三会返回 null）

如果对象定义了 toJSON 方法，调用 JSON.stringify 时会首先调用该方法，然后用它的返回值来序列化（字符串化）

> 如果一个对象含有非法 JSON 值的话可以这么做

可以传入一个参数 replacer，是一个数组或者函数，用来指定对象序列化中哪些属性应该被处理，哪些应该被排除。是数组的话只包含要处理的属性名称。是函数的话会对对象本身以及其各属性均调用一次，传入的参数为键和值，返回的值为指定的键值，是 undefined 的话则忽略这个键

### Object.create()

使用 Object.create(null) 创建的对象的 [[Prototype]] 属性为 null，并且没有 valueOf() 和 toString() 方法，因此无法进行强制类型转换

# 函数

js 中的函数是一等公民

## this

- 在全局环境中时指向 window
- 直接调用函数时，指向 window ，严格模式中是 undefined
- 作为对象的方法调用时指向对象本身
- 在构造函数中指向实例
- 使用 call bind apply 时指向第一个参数
- 箭头函数中指向函数外部作用域中的 this（也可以理解为箭头函数没有 this）
- getter/setter 的 this 指向调用时的对象（?）

## 原型
### prototype

只有函数有 prototype 属性，该属性指向一个对象，对象具有一个指向函数本身的 constructor 属性

### 原型链

试图访问对象的属性时，如果在对象上找不到就会沿着 `__proto__` 找（找到原型的 prototype），直到找到或者为 null（倒数第二层是 Object.prototype，之后就是 null）

> 注意和作用域链区分

### 无原型对象

```js
Object.create(null)
```

## 继承

[https://github.com/mqyqingfeng/Blog/issues/16]

### 原型链继承

```js
Child.prototype = new Parent() // 1
Child.prototype = Object.create(Parent.prototype) // 2
Child.prototype.__proto__ = Parent.prototype // 3
Object.setPrototypeof(Child.prototype, Parent.prototype) // 4
```

问题：

1. 引用类型的属性被所有实例共享

2. 在创建 Child 实例时不能向 Parent 传参

### 借用构造函数

```js
function Parent () {
  this.names = ['kevin', 'daisy']
}

function Child () {
  Parent.call(this)
}
```

优点：

1. 避免了引用类型的属性被所有实例共享（在上面的例子中 names 是不共享的）

2. 可以在 Child 中向 Parent 传参

缺点：

1. 无法继承 Parent.prototype 中的属性，方法都需要在构造函数中定义，每次创建实例都会创建一遍方法

### 组合继承

混合上述两种方式，属性在构造函数中定义，方法在 prototype 中定义，并在最后让 Child.prototype 指向 Parent.prototype

```js
function Parent (name) {
  this.name = name
  this.colors = ['red', 'blue', 'green']
}

Parent.prototype.getName = function () {
  console.log(this.name)
}

function Child (name, age) {
  Parent.call(this, name)
  this.age = age
}

Child.prototype = new Parent()
Child.prototype.constructor = Child
```

缺点：

1. 在 `Child.prototype = new Parent()` 中调用了一次 Parent ，在 `Parent.call(this, name)` 中又调用了一次，所以 Child.prototype 和 实例中都会有 color 属性（解决方法请看寄生组合式继承）

### 寄生式继承

创建一个仅用于封装继承过程的函数，该函数在内部以某种形式来做增强对象，最后返回对象

```js
function createObj (o) {
  var clone = Object.create(o)
  clone.sayName = function () {
    console.log('hi')
  }
  return clone
}
```

缺点：

1. 跟借用构造函数模式一样，每次创建对象都会创建一遍方法

### 寄生组合式继承

通过让 Child.prototype 间接访问到 Parent.prototype 的方式解决问题

```js
function Parent (name) {
  this.name = name
  this.colors = ['red', 'blue', 'green']
}

Parent.prototype.getName = function () {
  console.log(this.name)
}

function Child (name, age) {
  Parent.call(this, name)
  this.age = age
}

// 这里不一样
var F = function () {}
F.prototype = Parent.prototype
Child.prototype = new F()
Child.prototype.constructor = Child
```

### ES6 继承

```js
class Child extends Parent {
  // ...
}
```

#### 区别

- class 内部定义的方法是不可枚举的
- 不存在变量提升
- es5 的继承，是先创造子类实例对象 this ，再讲父类的方法添加到 this 上，class 继承则是先创造父类实例的 this （所以必须先调用 super 方法），然后再用子类的构造函数修改 this
- 子类的 `__proto__` 指向父类，子类原型的 `__proto__` 指向父类原型

## 闭包

- 闭包是javascript支持头等函数的一种方式，它是一个能够引用其内部作用域变量(在本作用域第一次声明的变量)的表达式，这个表达式可以赋值给某个变量，可以作为参数传递给函数，也可以作为一个函数返回值返回
- MDN：闭包是指那些能够访问自由变量的函数（自由变量是指在函数中使用的，但既不是函数参数也不是函数的局部变量的变量）
  - 由于函数访问全局变量也相当于在访问自由变量，所以技术角度上可以认为所有函数都是闭包（权威指南就这么说）
  - 实践角度：即使创建它的上下文已经销毁，它仍然存在；在代码中引用了自由变量
- 闭包是函数开始执行的时候被分配的一个栈帧，在函数执行结束返回后仍不会被释放(就好像一个栈帧被分配在堆里而不是栈里！)
- 即使父级函数的执行上下文已经在调用栈中被弹出了，闭包函数还是可以访问到父级函数的变量对象，是因为内部函数的作用域链中包含父级函数的变量对象
- 应用：柯里化、模拟私有变量或者方法

```js
var currying = function(fun) {
	//格式化arguments
	var args = Array.prototype.slice.call(arguments, 1);
	return function() {
    	//收集所有的参数在同一个数组中，进行计算
    	var _args = args.concat(Array.prototype.slice.call(arguments));
    	return fun.apply(null, _args);
	};
}
```

## new

## apply/call/bind

以及实现

# 类型

对 js 来说，变量没有类型（可以随时持有任何类型的指），值才有，语言引擎不会要求变量总是持有与其初始值同类型的值

## 内置类型

null, undefined, boolean, number, string, object, symbol

function 是内置类型 object 的子类型，它的内部属性 [[Call]] 让它成为可调用对象

## 抽象值转换操作
### ToString

- null, undefined, boolean：'...'
- symbol：'Symbol(...)'
- number：遵循规范 9.8.1
- object：返回 ToString(ToPrimitive) 的值

9.8.1 对数值类型应用 ToString：

1. 如果 m 是 NaN，返回字符串 "NaN"
2. 如果 m 是 +0 或 -0，返回字符串 "0"
3. 如果 m 小于零，返回连接 "-" 和 ToString (-m) 的字符串
4. 如果 m 无限大，返回字符串 "Infinity"
5. ...

### ToNumber

- true：1
- false, null：0
- 处理失败, undefined：NaN
- object：调用 ToNumber(ToPrimitive) 的值

### ToPrimitive

如果是对象则返回调用内部方法 [[DefaultValue]] 的结果，否则直接返回值本身

### [[DefaultValue]]

当用数字 hint 调用 O 的内部方法 [[DefaultValue]]：

```js
if (get valueOf) {
  var val = valueOf.call(O)
  if (typeof val === Primitive) return val
}
if (get toString) {
  var val toString.call(O)
  if (typeof val === Primitive) return val
}
throw new TypeError('Cannot convert object to primitive value')
```

当用字符串 hint 调用 O 的内部方法 [[DefaultValue]]：

```js
if (get toString) {
  var val = toString.call(O)
  if (typeof val === Primitive) return val
}
if (get valueOf) {
  var val valueOf.call(O)
  if (typeof val === Primitive) return val
}
throw new TypeError('Cannot convert object to primitive value')
```

> 会先找对象上的 valueOf 或 toString，没有再从原型上找

### ToBoolean

falsy：undefined, null, false, +0, -0, NaN, ''

## 强制类型转换

[https://juejin.im/entry/584918612f301e005716add6]

强制类型转换总是返回基本类型值

- to string：String, a.toString, a + ''
- to number：Number, parseInt, parseFloat, +a, - -a, +new Date(), 涉及位运算符的操作会执行抽象操作 ToInt32
- to boolean：Boolean, !!a, 条件判断, ||, &&
- trick：~a.indexOf(...), ~~a, a | 0

### ==/===

== 允许在比较时进行强制类型转换，而 === 不允许

11.9.3：

- 字符串和数字比较：总是将字符串 ToNumber
- 与布尔值比较：== 两边的布尔值会被强制转换成数字
- null 和 undefined 之间比较：true，与其他值比较都是 false（即使是假值）
- 与对象比较：将对象 ToPrimitive
- false, '', [] 在 == 两边的时候都会被转换成 0 

## 抽象关系比较

比较双方都是字符串：

1. 按字母顺序比较

其他情况：

1. 首先给双方调用 ToPrimitive
2. 如果出现非字符串就根据 ToNumber 将双方转换成数字
3. 出现 NaN 就返回 false
4. 比较大小

> 这里的 ToPrimitive 首先会调用 valueOf，但是一般不重写 valueOf 的话都是返回本身

### 问题来了!

- `{} >= {} -> true`：先将 {} 调用 ToPrimitive 转换成 '[object Object]'，再比较相等

## 判断类型
### NaN

Number.isNaN

# DOM

DOM模型用一个逻辑树来表示一个文档，树的每个分支的终点都是一个节点(node)，每个节点都包含着对象(objects)

## load/DOMContentLoaded

- DOMContentLoaded：浏览器已经完全加载了 HTML，DOM 树已经构建完毕，但是像是 `<img>` 和样式表等外部资源可能并没有下载完毕（由 document 对象触发）
- load：浏览器已经加载了所有的资源（图像，样式表等）
- beforeunload/unload：当用户离开页面时触发

> DOMContentLoaded 事件必须等待其所属 script 之前的样式表加载解析完成才会触发

### DOMContentLoaded 和脚本

js 主线程和 ui 渲染线程是互斥的，浏览器在解析 HTML 时遇到了 script 标签就无法继续构建 DOM 树，所以该事件有可能在所有脚本执行完后触发

外部脚本的加载（带 src 不带 async defer）也会暂停 DOM 树的构建

> 互斥的原因是 js 运行结果可能会影响 ui 线程的结果，js 执行时 GUI 线程会被挂起，并将 GUI 更新保存在一个队列中

Firefox, Chrome 和 Opera 会在 DOMContentLoaded 执行时自动补全表单

### DOMContentLoaded 和样式表

外部样式表虽然不会阻塞 DOM 解析，但是如果样式后有一个内联脚本，由于 js 可能需要获取 DOM 样式，就要等这个 CSS 加载完，而直到 css 加载完， js 执行完前都会阻塞 DOM 解析（css 阻塞 js，js 阻塞 DOM）

### 判断页面加载情况

document.readyState：

- loading：仍在加载（DOMContentLoaded 触发之前）
- interactive：文档已经完成加载，文档已被解析，但是诸如图像，样式表和框架之类的子资源仍在加载（DOMContentLoaded 触发）
- complete：文档和子资源加载完成（load 触发）

# 事件

 事件是在编程时系统内发生的动作或者发生的事情 —— 系统会在事件出现的时候触发某种信号并且提供一个自动加载某种动作的机制（比如运行代码）

## 模型

当一个事件发生在具有父元素的元素上时，现代浏览器运行两个不同的阶段 - 捕获阶段和冒泡阶段

默认情况下，所有事件处理程序都在冒泡阶段进行注册

### 捕获

浏览器检查元素的最外层祖先 `<html>` ，是否在捕获阶段中注册了一个事件处理程序，如果是，则运行它，之后再移动到下一个元素，直至到达实际触发事件的元素

### 冒泡

浏览器检查实际触发事件的元素是否在冒泡阶段中注册了事件处理程序，如果是，则运行它，之后再向直接祖先元素移动，直至到达 `<html>`

### 阻止默认行为

```js
e.preventDefault()
```

### 阻止冒泡

```js
e.stopPropagation()
```

### return false

同时阻止默认行为和冒泡（其实是直接阻止传播），只在 HTML 事件属性 和 DOM0 级事件处理方法中有效

### 委托

如果想要在大量子元素中单击任何一个都可以运行一段代码，可以将事件监听器设置在其父节点上

# Ajax

[https://segmentfault.com/a/1190000004322487]

## XMLHttpRequest

```js
var xhr = new XMLHttpRequest();
xhr.onload = reqListener;
xhr.open("get", "yourFile.txt", true);
xhr.send();
```

### readyState

0: (未初始化)还没有调用open()方法

1: (载入)已经调用open()方法，正在派发请求，`send()`方法还未被调用

2: (载入完成)send()已经调用，响应头和响应状态已经返回

3: (交互)响应体下载中; `responseText`中已经获取了部分数据

4: (完成)响应内容已经解析完成，用户可以调用

### status

服务器返回的状态码

### send()

- post 的时候可以传入 params，get/head 的时候参数一般设置为 null（即使传入也会忽略）
- 发送同步请求的时候：
  - xhr.timeout 必须为 0
  - xhr.withCredentials 必须为 false
  - xhr.responseType 必须为 ""
- 可以发送的类型：
  - ArrayBuffer
  - Blob
  - Document
  - DOMString
  - FormData
  - null
  - 参数的类型会影响 content-type header 的默认值

### response

- response：请求完成才会有值，否则会是 '' 或 null
- responseText：只能是文本数据
- responseXML：

### overrideMimeType

重写 response 的 content-type（用于指定数据类型，但是需要手动转换）

### setRequestHeader

`xhr.setRequestHeader(header, value)`

### getAllResponseHeaders

以及 getResponseHeader

只能获取被视为 safe 的字段

### withCredentials

默认情况下，浏览器在发送跨域请求时，不能发送任何认证信息（credentials），如果需要发送 cookie 的话需要将该项设置为 true

## xhr level2

- timeout/ontimeout
- xhr.send(formData)
- responseType：'blob', 'arraybuffer' （处理二进制数据）
- onprogress：event.loaded, event.total

# JavaScript 运行原理

引擎：调用栈（Call Stack）、Memory Heap

Web 环境：运行上下文（Runtime）、事件循环（Event Loop）

引擎负责整个代码的编译以及运行，编译器则负责词法分析、语法分析、代码生成等工作而作用域则负责维护所有的标识符（变量）

## Runtime

Web APIs：DOM、XHR、setTimeout() & Node APIs

## 执行上下文


# 模块
## 规范
### AMD（Asyncchronous Module Definition）

是 RequireJS 在推广过程中对模块定义的规范化的产出

异步加载（适合浏览器环境），通过回调，在加载完模块后执行依赖它的语句  

### CMD（Common Module Definition）

是 SeaJS 在推广过程中对模块定义的规范化产出

### CommonJS

- 每个模块内部，module 变量代表当前模块，所有模块都是 Module 的实例
- module 的 exports 属性是对外的接口，加载某个模块，其实是加载该模块的 module.exports 属性
- require：该方法读取一个文件并执行，最后返回文件内部的 exports 对象，require 的值是值拷贝，影响不到模块内部
- 模块可以多次加载，但是只会在第一次加载时运行一次，之后结果就缓存了（即使在其他模块中再次 require 也只会返回第一次加载的缓存）
- 循环加载：如果发生模块的循环加载，即A加载B，B又加载A，则B将加载A的不完整版本（只输出已经执行的部分）

### ES2015 Module

- 模块中的代码自动运行在严格模式下，其中的顶级变量不会添加到全局作用域中
- import 会在编译期间提升到模块头部
- 由于引擎需要静态决定 import 和 export 的内容，两者只能在顶级作用域下使用
- import 语句只创建了只读变量（是引用的），但是 import 而来的函数可以修改与其同一个模块中定义的变量
- 关于在浏览器中引入：使用 script 标签（type="module"，总是 defer）的 src 属性或者在 script 内联代码中 import
- 循环加载：遇到模块加载命令 import 时（如果不是循环加载的情况就正常执行需要加载的模块），不会去执行模块，而是只生成一个引用，等到真的需要用到时，再到模块里面去取值（因为是动态引用，没有缓存值）

```js
/* 模块语法 */
export let name = 'neko'

export function sum (a, b) {
  return a + b
}

function multiply (a, b) {
  return a * b
}
export { multiply }

/* 重命名，import 同样可以 */
export { sum as add }

/* 输出默认值，只能给模块设定一个默认值，不必须命名 */
export default function (a, b) {
  return a + b
}
export default sum
export { sum as default }

/* 类似于 const ，不可再次定义同名变量 */
import { identifier1, identifier2 } from './example.js'

/* 将整个模块引入，所有的输出可以以变量的形式访问 */
import * as example from './example.js'

/* 多次 import 同样的模块，该模块也只会执行一次，第一次 import 之后，其实例化的模块会驻留在内存中并随时可由另一个 import 语句引用 */

/* 引入默认值，需要注意的是没有使用花括号 */
import sum from './example.js'

/* 同时引入 */
import sum, { color } from "./example.js"
import { default as sum, color} from "./example.js"

/* 再输出 */
export { sum } from "./example.js"

/* 全局引入 */
/* 一些模块可能并不输出任何内容，相反，他们只是修改全局作用域内的对象（他们是可以访问到全局作用域的） */
Array.prototype.pushAll = function (items) {
  return this.push(...items)
}
/* 然后引入即可 */
import "./example.js"
```

## 问题
### AMD 和 CMD 的区别

- 对于依赖的模块，AMD 是提前执行（不过 RequireJS 在 2.0 之后也改成可以延迟执行），CMD 是延迟执行（lazy loading?）
- AMD 直接加载完依赖再执行回调，CMD 是在执行需要依赖的代码前再加载这个依赖
- AMD 的 API 默认是**一个当多个用**，CMD 的 API 严格区分，推崇**职责单一**。比如 AMD 里，require 分全局 require 和局部 require，都叫 require。CMD 里，没有全局 require，而是根据模块系统的完备性，提供 seajs.use 来实现模块系统的加载启动。CMD 里，每个 API 都**简单纯粹**

### CommonJS 与 ES2015 Modules 的区别

- [https://www.zhihu.com/question/56820346]
- [http://es6.ruanyifeng.com/#docs/module-loader]
- [https://github.com/olifer655/commonJS/issues/6]
- CommonJS 模块输出的是一个值的拷贝，ES6 模块输出的是值的引用
- CommonJS 模块是运行时加载，ES6 模块是编译时（静态编译）输出接口（ES6 入门）

### 为什么在 webpack 中 import 和 require 都可以使用? 

CommonJS 由 webpack 默认支持，而 import 由 babel 支持

### 循环依赖

```js
// a.js
console.log('a starting');
exports.done = false;
const b = require('./b');
console.log('in a, b.done =', b.done);
exports.done = true;
console.log('a done');

// b.js
console.log('b starting');
exports.done = false;
const a = require('./a');
console.log('in b, a.done =', a.done);
exports.done = true;
console.log('b done');

// node a.js
// 执行结果：
// a starting
// b starting
// in b, a.done = false
// b done
// in a, b.done = true
// a done

// a.js
console.log('a starting')
import {foo} from './b';
console.log('in b, foo:', foo);
export const bar = 2;
console.log('a done');

// b.js
console.log('b starting');
import {bar} from './a';
export const foo = 'foo';
console.log('in a, bar:', bar);
setTimeout(() => {
  console.log('in a, setTimeout bar:', bar);
})
console.log('b done');

// babel-node a.js
// 执行结果：
// b starting
// in a, bar: undefined
// b done
// a starting
// in b, foo: foo
// a done
// in a, setTimeout bar: 2
```

# 高级
## 严格模式

  - 必须要事先声明变量
  - 直接使用函数调用（不是作为方法）的时候，this 是 undefined （而不是 window）
  - 禁止给函数提供多个相同的参数
  - 去除了 with 语句
  - 会使引起静默失败的赋值语句抛出错误
  - 禁止八进制数字语法（在 es6 中需要在数字前面加上 0o）
  - 禁止设置 primitive 值的属性
  - eval 不再为上层范围引入新变量（eval 不会在当前作用域中声明变量（虽然 eval 能访问到当前作用域），仅为运行的代码创建变量）
    - eval 具有某种意义上的动态作用域
    - 将 eval 赋值给其他变量，调用的时候是在全局的作用域中执行（而不是当前）
  - 禁止删除变量声明 （var x; delete x;）
  - 在非严格模式中 arguments 和函数参数完全绑定在一起，会一起修改，严格模式下不会（符合对指针的理解）
  - 不再支持 arguments.callee（指向当前正在进行的函数）和 func.caller
  - this 不再会被强制转换为对象（包装对象），并且未指定的 this 会变成 undefined
  - 禁止了不在脚本或者函数层面的函数声明（在条件、循环语句中声明）

## 类型化数组（Typed Arrays）

是一种类似数组的对象，用来处理来自网络协议、二进制文件格式和原始图形缓冲区等源的二进制数据，还可用于管理具有已知字节布局的内存中二进制数据（MSDN）

由于是直接操作二进制数据，效率比常规的数组更高，适合于有大量二进制数据操作需求的场合

为了达到最大的灵活性和效率，类型数组将实现拆分为缓冲（ArrayBuffer）和视图（类型数组视图或DataView）两部分

### ArrayBuffer

是一种数据类型，用来表示一个通用的、固定长度的二进制数据缓冲区（没有机制获取数据，仅提供缓冲作用），需要使用视图来访问其中的内容

### 类型数组视图（TypedArray?）

具有自描述性的名字和所有常用的数值类型像 Int8，Uint32，Float64 等等

是类数组，需要某些特定数组操作的时候可以使用 Array.from 转换为数组

| 数据类型 | 字节长度 | 含义 | 对应的C语言类型 |
| ------- | ---- | ---------------- | -------------- |
| Int8    | 1    | 8位带符号整数          | signed char    |
| Uint8   | 1    | 8位不带符号整数         | unsigned char  |
| Uint8C  | 1    | 8位不带符号整数（自动过滤溢出） | unsigned char  |
| Int16   | 2    | 16位带符号整数         | short          |
| Uint16  | 2    | 16位不带符号整数        | unsigned short |
| Int32   | 4    | 32位带符号整数         | int            |
| Uint32  | 4    | 32位不带符号的整数       | unsigned int   |
| Float32 | 4    | 32位浮点数           | float          |
| Float64 | 8    | 64位浮点数           | double         |

### DataView

是一种底层接口，它提供有可以操作缓冲区中任意数据的读写接口，适合与操作不同类型数据的场景（不像 TypedArray 局限于某一种类型的数据）

### 应用

- FileReader API(readAsArrayBuffer方法)
- XMlHttpRequest的send方法(支持类型数组作为参数)
- Canvas

说明：类型数组并不是支持所有的原生数组的API(比如push和pop就不可用，因为ArrayBuffer给定了字节数，TypedArray视图自然无法调用)

## Web Worker

[http://www.alloyteam.com/2015/11/deep-in-web-worker/]

[https://www.html5rocks.com/zh/tutorials/workers/basics/]

Web Workers是一种机制，通过它可以使一个脚本操作在与Web应用程序的主执行线程分离的后台线程中运行。这样做的优点是可以在单独的线程中执行繁琐的处理，让主（通常是UI）线程运行而不被阻塞/减慢

Web Worker 规范中定义了两类工作线程，分别是专用线程Dedicated Worker和共享线程 Shared Worker，其中，Dedicated Worker只能为一个页面所使用，而Shared Worker则可以被多个页面所共享

## 垃圾回收

语言内嵌的 “垃圾回收器” 跟踪内存的分配和使用，以便分配的内存不再使用时自动释放它，但也只能由有限制地解决一般问题

### 引用计数

如果没有引用指向该对象（零引用），对象将被垃圾回收机制回收

对象对原型的引用是隐式的、对属性的引用是显式的，所以遇到两个对象之间循环引用的情况就无法回收，这样就容易发生内存泄漏

### 标记清除

根据对象是否可以获得来决定是否不再需要（现代浏览器采用）

垃圾回收器在运行的时候会给存储在内存中的所有变量都加上标记。然后，它会去掉环境中的变量以及被环境中的变量引用的变量的标记（闭包）。而在此之后再被加上标记的变量将被视为准备删除的变量，原因是环境中的变量已经无法访问到这些变量了。最后，垃圾回收器完成内存清除工作，销毁那些带标记的值并回收它们所占用的内存空间

垃圾回收器定期从根（全局对象）开始，找所有从根开始引用的对象，然后找这些对象引用的对象
  
### 内存泄漏

- 无意的全局变量（意外的 this 指向 window）
- 未 clearInterval 或者 clearTimeout
- IE6-8 在 DOM & BOM 等 COM 对象循环绑定时会发生泄漏，需要切断循环引用，将对对象的应用设置为 null
- IE7-8 会发生 XMLHttpRequest 泄漏，需要手动设置其实例为 null
- 定时器（严格上说不能算是泄露，是被闭包持有了，是正常的表现），对于闭包中无用的变量可以使用 delete 操作符进行释放
- removeChild 删除节点只是在 DOM 树中删除
