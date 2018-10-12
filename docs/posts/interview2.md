# HTML
## 常见的替换元素

img，object，video，textarea，input，iframe，canvas

多数没有内容且没有子元素。

## 行内元素有哪些？块级元素有哪些？ 空(void)元素有那些？

- 行内: input,span,a,img以及display:inline的元素
- 块级: p,div,header,footer,aside,article,ul以及display:block这些
- void: br,hr

# CSS
## 可继承样式

下面的继承属性总结来源于网络，出处已不可考。

- 不可继承的：display、margin、border、padding、background、height、min-height、max- height、width、min-width、max-width、overflow、position、left、right、top、 bottom、z-index、float、clear、table-layout、vertical-align、page-break-after、 page-bread-before和unicode-bi`di。
- 所有元素可继承：visibility和cursor。
- 内联元素可继承：letter-spacing、word-spacing、white-space、line-height、color、font、font-family、font-size、font-style、font-variant、font-weight、text- decoration、text-transform、direction。
- 块状元素可继承：text-indent和text-align。
- 列表元素可继承：list-style、list-style-type、list-style-position、list-style-image。
- 表格元素可继承：border-collapse。
- TL;DR：跟布局有关的，宽高、vertical-align、display、position、float、z-index、margin、padding、border 等不可继承。跟字体有关的，line-height、font-xx 等，以及 visibility、cursor、list-style 等都可以继承。

## 选择器权重

- 1,0,0,0：内联样式
- 0,1,0,0：id 选择器
- 0,0,1,0：类、伪类、属性选择器
- 0,0,0,1：元素、伪元素选择器
- 0,0,0,0：结合符、通配选择器

## 盒模型

元素在文档中是以一个矩形盒子来显示，盒子包含多个框，从内到外分别是 content, padding, border, margin。

盒子的宽高默认为 content-box 的宽高，但是可以通过 box-sizing 属性来修改为 border-box。

盒子的背景默认延伸到 border-box 下面（会被 border 挡住），但是可以通过 background-clip 属性修改为其他。

## margin 合并

两个或多个毗邻的普通流中的块元素垂直方向上的 margin 会重叠。

按以下标准得出距离：

- 两正，取最大者；
- 一正一负，则相加；
- 两负，取最小者。

父子元素之间的 margin 合并，会合并到父元素上。兄弟元素则是两者之间的间距。

### 清除合并

父子元素：触发父元素的 BFC、父元素拥有 padding 或 border。

兄弟元素：两者之间有其他元素。

## 浮动
### 清除浮动

使用 clear 属性让元素不与浮动元素相邻，只能应用于 block 元素。

### 闭合浮动

使浮动元素的包含块高度增加以包含其内的所有浮动元素，具体有两类方法：

1. 触发父元素BFC（Block Format Context）
2. clear: both;
  - clearfix
  - 在底下加一个 div 或 br 元素设置 clear: both;

```css
.clearfix:after {
  content: '';
  clear: both;
  display: block;
  /* visibility: hidden;
  font-size: 0;
  height: 0; */
}
```

### BFC

- 独立的布局单元（不影响其他位置）
- 想象该元素在单独的 iframe 里渲染
- 触发了 BFC 的元素不会跟其他元素产生重叠（主要是浮动元素）
  - 如果是的话会变窄或下移以避开浮动元素（算是清除浮动?）
- 触发了 BFC 的元素也会包裹其所有后代元素（定位元素除外）
  - 包括浮动元素（闭合浮动）

触发：

- float: left / right;  
- overflow: hidden / auto / scroll;
- position: absolute / fixed;
- display: flow-root / table-cell / table-caption / inline-block / flex / inline-flex;
- zoom: 1;（for IE）

## 定位位置

根据包含块来判断：

- static / relative 时包含块是最近祖先块元素或格式化上下文的 content-box
- absolute 时包含块是最近定位元素的 padding-box
- fixed 时包含块是 viewport
- 特殊情况！！
  - position / fixed 时，如果父元素满足：transform / perspective / filter / will-change: transform or perspective or filter; 时，包含块会是这个父元素的 padding-box
- 基于自身定位时定位相对于自身 margin-box

## line-height

单位为 ex / em / % 时相对于父元素 font-size，子元素继承父元素的 line-height。

无单位时由自身（包括子元素）font-size 计算得来（推荐）。

## css 三角形

```css
.triangle {
  display: inline-block;
  width: 0;
  height: 0;
  border: 50px solid;
  border-color: #f00 transparent transparent;
  border-radius: 50%; /* 扇形 */
}
```

## 布局
### 水平居中

1. 父：text-align: center; 子：display: inline-block;
2. 子：display: table; margin: 0 auto;
3. 子：display: block; width: 100px; margin: 0 auto;
4. 父：position: relative;（或者其他让子元素能基于其定位的） 子：absolute + transform
5. 父：display: flex; justify-content: center;

### 垂直居中

1. 父：display: table-cell; vertical-align: middle;
2. absolute + transform
3. 父：display: flex; align-items: center;

### 多列定宽，一列自适应

1. 左：float-left; 右：margin-left；
2. 左：float: left; 右: overflow: hidden;（display: flow-root; flex;）（适合不定宽）
3. parent：display: table; width: 100%; 子：display: table-cell;
4. parent： display: flex; 右：flex: 1;

### 等高布局

```css
/* 1 */
.parent{
	overflow: hidden;
}
.left,.right{
	padding-bottom: 9999px;
	margin-bottom: -9999px;
}

/* 2 */
.parent {
  display: table;
  width: 100%;
}
.child {
  display: table-cell;
}

/* 3 */
.parent {
  display: flex;
  width: 100%;
}
```

## flex

设为 Flex 布局以后，子元素的float、clear和vertical-align属性将失效。

使用 flex 布局的元素称为容器，其子元素称为项目（item）。容器存在水平主轴（main axis）和垂直交叉轴（cross axis），项目排在轴线上（沿着 cross axis）。

### 容器属性

- flex-direction：决定主轴方向（row）
- flex-wrap：规定了轴线上的项目排不下的话如何换行（nowrap）
- flex-flow：是 flex-direction 属性和 flex-wrap 属性的简写形式（row nowrap）
- justify-content：规定了项目在 main axis 上的对齐方式（flex-start）
- align-items：规定了项目在轴线上的对齐方式（stretch）
- align-content：规定了多根轴线的对齐方式（如果只有一根就无效）（stretch：占满 cross axis）

### 项目属性

- order：定义项目的排列顺序，数字越小越靠前（0）
- flex-grow：定义项目的放大比例（0：存在剩余空间也不放大）
  - 设置了大于 0 的值之后就会按比例占据剩余空间
  - 剩余空间指容器轴线长去除 flex-basis 之后的空间（grow 之后将按比例分配的空间加在自身 flex-basis 上）
  - 不会缩小
- flex-shrink：定义项目的缩小比例（1：空间不足会缩小）
  - 同样是按超出空间计算，之后按比例减在自身 width 上，数值越高按比例减的越多
- flex-basis：定义了在分配多余空间之前，项目占据的主轴空间（auto：默认和 width 一致）
- flex：是 flex-grow, flex-shrink 和 flex-basis 的简写（0 1 0%），后 2 可省略
  - 只填一个值的时候 flex-basis 是 0，所以不会把宽度计入剩余空间
- align-self：让项目能拥有与 align-items 不同的对齐属性

## Repaint & Reflow
### 重绘

由于节点的几何属性发生改变或者由于样式发生改变，例如改变元素背景色时，屏幕上的部分内容需要更新。

### 回流

元素的几何属性和位置变化导致部分渲染树（或者整个渲染树）需要重新分析并且节点尺寸需要重新计算。

一般的流式布局，只需遍历一次就可以完成渲染树的计算，但 table 及其内部元素可能需要多次计算才能确定好其在渲染树中节点的属性，通常要花 3 倍于同等元素的时间。

### 队列更新

元素样式改变的时候，浏览器会通过将更新加入队列来批量更新，优化重排过程。但是用 js 获取布局信息会导致直接触发队列更新：

offsetTop（scroll, client）, offsetLeft, offsetWidth, offsetHeight, getComputedStyle

### 触发

- 添加、删除、更新 DOM 节点
- 通过 display: none 隐藏一个 DOM 节点-触发重排和重绘
- 通过 visibility: hidden 隐藏一个 DOM 节点-只触发重绘，因为没有几何变化
- 移动或者给页面中的 DOM 节点添加动画
- 添加一个样式表，调整样式属性
- 用户行为，例如调整窗口大小，改变字号，或者滚动

### 减少方法

- 使用 translate 替代 top（transfrom 创建新图层）。
- 使用 visibility 替换 display: none ，因为前者只会引起重绘，后者会引发回流（改变了布局）。
- 把 DOM 离线后修改，比如：先把 DOM 给 display:none (有一次 Reflow)，然后你修改100次，然后再把它显示出来。
- 不要把 DOM 结点的属性值放在一个循环里当成循环里的变量（获取 offsetTop 会导致回流，因为需要马上获取布局信息）。
- 不要使用 table 布局，可能很小的一个小改动会造成整个 table 的重新布局。
- 动画实现的速度的选择，动画速度越快，回流次数越多，也可以选择使用 requestAnimationFrame。
- CSS 选择符从右往左匹配查找，避免 DOM 深度过深。
- 将频繁运行的动画变为图层，图层能够阻止该节点回流影响别的元素。

## 硬件加速

- translate3d / rotate3d / scale3d
- translateZ

当使用CSS transforms 或者 animations时可能会有页面闪烁的效果，可以使用transform: translate3d(0, 0, 0)修复。

可能会导致性能问题。

## 行内元素 padding margin

水平方向有效，垂直方向无效。

## BOM

### navigator
### screen
### location
### history

## DOM
### 属性节点

hasAttribute, getAttribute, setAttribute, removeAttribute, dataset

### 文本节点

```js
innerText // 不能返回 script 标签里面的源码，忽略多余的空白并试图保留表格格式，只保留换行符
textContent // 保留全部的空白和换行等文本格式
```

### 遍历节点

```js
parentNode // 返回当前结点的父节点
offsetParent // 返回的是离当前结点最近的有定位的父节点

childNodes // 返回当前结点的全部子节点，包括属性??和文本
children // 只返回子元素集合，不包括文本
firstElementChild // 元素的第一个子元素节点
lastElementChild // 表示元素的最后一个子元素节点

nextSibling
nextElementSibling
previousSibling
previousSibling
```

### 操作 CSS 样式

```js
style // 返回 CSSStyleDeclaration 对象，包含元素所有样式（默认值为 ''），只能获得元素的行内样式
getComputedStyle(element, [pseudoElt]) // 返回一个对象，该对象在应用活动样式表并解析这些值可能包含的任何基本计算后报告元素的所有CSS属性的值
className // 最好还是通过修改 className 来 toggle 样式
```

### 构造元素节点

```js
document.createElement()
document.createTextNode()
document.createComment()
document.createDocumentFragment()
node.cloneNode(true) // 传入 true 的话拷贝自身加子节点，传入 false 只拷贝自身
```

### 操作节点

```js
parent.appendChild(node)
parent.insertBefore(node, siblingNode)
element.insertAdjacentHTML(position, text) // 将指定的文本解析为HTML或XML，并将结果节点插入到DOM树中的指定位置。它不会重新解析它正在使用的元素，因此它不会破坏元素内的现有元素。这避免了额外的序列化步骤，使其比直接innerHTML操作更快。

parent.removeChild(siblingNode)
parent.replaceChild(node, siblingNode)
```

# JS
## 作用域

收集和维护了所有声明的变量，用来确定当前执行代码可以访问到哪些变量。

js 使用词法作用域，函数内部能访问哪些变量取决于函数在代码中的位置，函数可以访问到定义这个函数的代码块能访问到的变量，以及函数内部变量。

## 作用域链

由当前环境和上层环境的变量对象组成，保存在函数内部的 [[Scope]] 属性中。从内至外，最外层是全局变量对象，这样就可以保证对执行上下文有权访问的所有变量的有序访问。

在创建函数的时候会预先创建一个包含全局变量对象（是全局还是父？）的作用域链，之后执行函数，创建执行上下文时会复制这个 [[Scope]]，并进行补充，添加自身变量对象。

查找变量的时候，会首先从当前执行上下文的变量对象中查找，如果没有找到就会从父级执
行上下文的变量对象中查找，一直向上至全局上下文的变量对象。

本来的话，函数执行完毕之后活动对象就会被销毁，但是闭包会让内部函数的作用域链中包含外部函数的活动对象，由于仍存在引用，外部函数执行完毕后它的活动对象也不会被销毁（当然执行环境的其他东西都会被销毁）。

## 原型

prototype：每个函数都有一个 prototype 属性，指向一个对象，对象具有一个 constructor 属性，指向函数本身。

## 原型链

每个对象都有一个 `__proto__` 属性指向其构造函数的原型（prototype）。访问对象的属性时，如果在对象上找不到就会沿着 `__proto__` 找，直到找到或者为 null 为止。

## 执行上下文

执行函数的时候，首先会创建一个执行上下文，用以确定执行函数时所需要的 [[Scope]], 变量对象（arguments, 变量, 函数）, this。

执行环境：全局代码、函数代码、Eval 代码

1. 函数创建阶段
  - 预先创建包含全局变量对象的作用域链，并保存在 [[Scope]] 属性中（这时候还和执行上下文无关）

2. 执行函数代码之前
  - 先创建执行上下文，包含函数执行过程中要用到的：变量对象、作用域链、this

3. 找到调用函数的代码并在执行代码之前的执行上下文创建阶段
  - 复制函数中的 [[Scope]] 对象，并在活动对象创建完后将其推入作用域前端
  - 创建变量对象（包括 arguments、变量、函数）
    - 创建 arguments，检查上下文，初始化参数名称和值并创建引用的复制（有值）
      - 一开始 AO 只包含 arguments，之后才加入形参、函数、变量（是这个顺序）
    - 扫描函数声明，会创建对应变量名指向该函数，如果已经存在用新的引用值覆盖
    - 扫描变量声明，同样会创建对应变量名，但这个时候值还是 undefined，如果已经存在不会进行任何操作（所以其实也不会声明两次，第二次直接忽略）
  - 决定 this 指向

4. 执行代码阶段
  - 执行环境是函数的话，变量对象会变成活动对象
  - 重新扫描一遍代码，执行代码（在这个时候给变量赋值）

5. 执行完毕
  - 上下文会被销毁，保存在其中的所有变量和函数定义也随之销毁（全局执行环境只有关闭网页才会销毁）

## 闭包

函数 A 返回了一个函数 B，并且函数 B 中使用了函数 A 的变量，函数 B 就被称为闭包。其实就是词法作用域的原因，让函数可以访问到定义这个函数的代码块中的所有变量和函数内部变量。

闭包原理：即使父级函数的执行上下文已经在调用栈中被弹出了，闭包函数还是可以访问到父级函数的变量对象，是因为内部函数的作用域链中包含父级函数的变量对象。

还有一种说法是因为函数 A 中的变量这时候是存储在堆上的。现在的 JS 引擎可以通过逃逸分析辨别出哪些变量需要存储在堆上，哪些需要存储在栈上。

## Array.sort

V8 引擎的 sort 方法，在 10 以内是插入排序，10 以上（注释里写的是 22）是快速排序（由于快排不稳定，所以不太适合直接使用 sort 来排之前已经排序好的数据）。

## 事件流

捕获：浏览器从元素最外层祖先（html）开始检查是否注册事件，是则运行，直至到达实际触发事件元素。

冒泡：浏览器从实际触发事件元素开始检查是否注册事件，是则运行直至到达 html。

两者的顺序：捕获 -> 处于目标 -> 冒泡。

所以如果在父元素上绑定两种事件，会先触发父元素的捕获事件（true），再是冒泡事件（false）。但是子元素（就是触发事件的元素）触发事件的顺序是按 addEventListener 的注册先后来，因为这个时候和捕获冒泡无关，而是在处于目标阶段。

## Ajax

[readme](https://segmentfault.com/a/1190000004322487)

```js
var xhr = new XMLHttpRequest()
xhr.onreadystatechange = function () {
  if (xhr.readyState === 4) {
    if (xhr.status === 200) {
      // code
    } else {
      // err
    }
  }
}
xhr.open('GET', url)
xhr.setRequestHeader('Content-Type', 'application/json')
xhr.send(null)
```

## 获取长度为 n 的数组

- Array.from({length: 100})
- [...Array(100)]
- Array.apply(null, {length: 100})
- [].concat.apply([], {length: 100})

## bind

```js
Function.prototype.bind = function(context, ...res) {
  let self = this
  return function(...arg) {
    return self.apply(context, [...res,...arg])
  }
}
```

## 数组去重

使用 includes 可以识别 NaN。

```js
// 均无法去重对象，除 set、map 方法均无法去重 NaN

// 双重循环
function unique (array) {
  const res = []
  for (let i = 0; i < array.length; i++) {
    const cur = array[i]
    if (res.indexOf(cur) === -1) res.push(cur)
  }
  return res
}

// 排序后去重
function unique (array) {
  const sortedArray = array.sort((a, b) => a - b)
  const res = []
  let seen
  for (let i = 0; i < sortedArray.length; i++) {
    if (i === 0 || seen !== sortedArray[i]) {
      res.push(sortedArray(i))
    }
    seen = sortedArray(i)
  }
  return res
}

// 使用 map 判断是否重复
function unique(array) {
  const obj = {};
  return array.filter(function(item, index, array) {
    return obj.hasOwnProperty(typeof item + item) ? false : (obj[typeof item + item] = true)
  })
}

// Set
[...new Set(array)]
Array.from(new Set(array))

// Map
function unique (array) {
  const map = new Map()
  return array.filter(item => !map.has(item) && map.set(item, 1))
}
```

## bind

```js
Function.prototype.myBind = function(thisArg, ...bindedArgs) {
  var self = this
  return function f(...args) {
    if (this instanceof f) {
      Object.setPrototypeOf(this, self.prototype)
      return self.call(this, ...bindedArgs, ...args)
    }
    return self.call(thisArg, ...bindedArgs, ...args)
  }
}
```

## memoization

```js
const memoize = fn => {
  const cache = new Map()
  return value => {
    const cachedResult = cache.get(value)
    if (cachedResult !== undefined) return cachedResult
    const result = fn(value)
    cache.set(value, result)
    return result
  }
}
```

## 判断数组

```js
Array.isArray()

Object.prototype.toString.call()

// 其他
constructor, instanceof
```

## 对象复制
### 浅拷贝

```js
let b = Object.assign({}, a)

let b = {...a}

// for in 遍历
```

### 深拷贝

JSON.stringify：undefined, function, symbol, RegExp, 循环引用无法处理

```js
function deepClone (value) {
  const parents = []
  const children = []
  return clone(value)
  
  function clone (value) {
    if (!value || typeof value !== 'object') return value
  
    const ctor = value.constructor
    let obj
  
    switch (ctor) {
      case RegExp:
        obj = new ctor(value)
        break;
      case Date:
        obj = new ctor(value.getTime())
        break;
      default:
        obj = new ctor()
    }

    // 处理循环引用
    const index = parents.indexOf(value)
    if (index !== -1) {
      return children[index]
    }
    parents.push(value)
    children.push(obj)
  
    for (let key in value) {
      if (value.hasOwnProperty(key)) {
        obj[key] = clone(value[key])
      }
    }

    return obj
  }
}
```

## 类数组或可迭代对象转换为数组

```js
Array.from(arrayLikeObject)
[].slice.call(arrayLikeObject)
[].concat.call(arrayLikeObject)
```

## 继承

```js
// 组合寄生继承
function Child() {
  Parent.call(this)
}

var F = new function () {}
F.prototype = Parent.prototype
Child.prototype = new F()
Child.prototype.constructor = Child

// 2
function Child() {
  Parent.call(this)
}

Child.prototype = Object.create(Parent.prototype, {
  constructor: {
    value: Child,
    enumerabel: false,
    writable: true,
    configurable: true
  }
})

// class
class Child extends Parent {}
```

### es5 继承和 class 继承的区别

1. class内部方法不可枚举（包括原型上的方法和静态方法，当然实例上的是可以的，因为直接通过赋值添加）

2. class不存在变量提升

3. 继承时，子类的 `__proto__` 指向父类，子类原型的 `__proto__` 指向父类原型

4. 构造函数中的 this 区别
- es5：子类构造函数中的 this 在一开始就是子类的实例 ，之后再调用Parent.call(this) 赋值。
- class：子类构造函数在一开始就调用super()，在其中创建父类的实例并且给this赋值，之后再在子类构造函数中对其修改。

## arguments

- 是所有非箭头函数中都可用的局部变量
- 类数组，除 length 和索引元素之外没有任何 Array 属性，但是可以被转换成一个真正的 Array
- 对 arguments 使用 slice 会阻止引擎对其优化

## 箭头函数

- this：根据声明函数位置的词法作用域来决定this值
- arguments：没有
- prototype：没有
- 不能被 new 调用

## new

- 创建一个实例对象，并让构造函数中的 this 指向该对象（模拟实现的话只能使用 call）
- 将实例对象的 `__proto__` 属性指向原型对象的 `prototype`
- 调用构造函数，如果返回值不是对象的话返回 this，否则返回返回值

## 进制转换

- parseInt('010', 2)
- 114514..toString(2)

## Event Loop

简单来说就是主线程不断循环读取任务队列中的事件的机制。主线程会监听调用栈，当调用栈为空的时候就会从任务队列去出任务加入到调用栈中执行，之后再弹出，不断循环。

任务队列分为 microtask 和 macrotask，前者只有一个，后者有多个。

每一个进程都会有一个 Event Loop。

## 异步循环

使用 for-of、for

自己实现一个 forEach

异步 reduce？

## require 的实现

简单的讲实现就是先声明一个 module = { exports: {} } 和 exports = module.exports，之后获取文件中的代码，并用一层自执行函数包装（字符串拼接），传入 require, module, exports, __dirname, filename，再执行它，这个时候就会修改传入的 module 和 exports，最后将 module.exports 返回。

缓存：用 require.cache 缓存。

```js
if (require.cache[filename]) {
  return require.cache[filename].exports
}

...

require.cache[filename] = module
return module.exports
```

为什么还需要一个 module，只用 exports 不行：因为在代码中给 exports 赋值（想 export 基本值的话）只会指向新的引用，不会修改到传入的 exports。

```js
function require(/* ... */) {
  const module = { exports: {} };
  ((module, exports) => {
    // 模块代码在这。在这个例子中，定义了一个函数。
    function someFunc() {}
    exports = someFunc;
    // 此时，exports 不再是一个 module.exports 的快捷方式，
    // 且这个模块依然导出一个空的默认对象。
    module.exports = someFunc;
    // 此时，该模块导出 someFunc，而不是默认对象。
  })(module, module.exports);
  return module.exports;
}
```

# 数据结构 & 算法
## 二分查找

。

## 排序算法

```js
function swap (ary, left, right) {
  [ary[left], ary[right]] = [ary[right], ary[left]]
}

// 冒泡：复杂度 O(n * n)，稳定
function bubble (ary) {
  for (let i = 0; i < ary.length; i++) {
    let tag = true
    for (let j = 0; j < ary.length; j++) {
      if (ary[j] > ary[j + 1]) {
        swap(ary, j, j + 1)
        tag = false
      }
    }
    if (tag) return ary
  }
  return ary
}

// 插入排序：复杂度 O(n * n)，稳定
// i 左边是已经排好序的数组，每次循环 j 从后向前扫描，j 比 j + 1 大就交换位置
function insertion (ary) {
  for (let i = 1; i < ary.length; i++) {
    for (let j = i - 1; j >= 0 && ary[j] > ary[j + 1]; j--) {
      swap(ary, j, j + 1)
    }
  }
  return ary
}

// 选择排序：复杂度 O(n * n)，不稳定
// 找出最小的放在最左边
function selection (ary) {
  for (let i = 0; i < ary.length - 1; i++) {
    let min = i
    for (let j = i + 1; j < ary.length; j++) {
      if (ary[j] < ary[min]) min = j
    }
    swap(ary, i, min)
  }
  return ary
}

// 归并排序：复杂度 O(N * logN)，稳定
// 递归地将数组分成两堆至最多包含两个元素并排序，之后合并
function mergeSort (ary) {
  if (ary.length <= 1) return [...ary]

  const mid = ary.length / 2 | 0
  const left = mergeSort(ary.slice(0, mid))
  const right = mergeSort(ary.slice(mid))
  const res = []

  let i = 0, j = 0
  while (i < left.length && j < right.length) {
    if (left[i] <= right[j]) res.push(left[i++])
    else res.push(right[j++])
  }

  return res.concat(left.slice(i), right.slice(j))
}

// 原地快排：复杂度 O(logN)，不稳定
// 每次按 pivot 分成比 pivot 小的一堆和比 pivot 大的一堆，i 的位置表示小值末尾
// 先把 pivot 放到末尾是因为最后肯定会换到 ++i 的位置，这样 i 左边就必定是 <= pivot 的值
function quickSort (ary, start = 0 ,end = ary.length - 1) {
  if (end <= start) return

  const pivotIndex = Math.floor((end - start + 1) * Math.random()) + start
  const pivot = ary[pivotIndex]
  let i = start - 1, j = start

  swap(ary, pivotIndex, end)

  for (; j <= end; j++) {
    if (ary[j] <= pivot) {
      swap(ary, ++i, j)
    }
  }

  quickSort(ary, start, i - 1)
  quickSort(ary, i + 1, end)

  return ary
}

// 堆排序：复杂度 O(logN)，不稳定
// 二叉树排序
```

# 浏览器
## 网络解析过程
### DNS 域名解析

输入的 url 由于是域名，TCP/IP 使用 IP 地址来唯一确定一台主机到因特网的连接，需要使用 DNS 来完成域名到 IP 地址的映射工作。

流程：

- 缓存
  - 浏览器缓存 -> 本地host -> 路由器缓存：找到即可
- 服务器查询
  - 本地 DNS 服务器：由 DNS 解析器向本地 DNS 服务器发起 UDP 请求，服务器首先查询是否在本地区域文件或者 DNS 缓存中，再根据是否设置转发器决定是向上一级 DNS 服务器还是根 DNS 服务器发送**代理**请求，最后将应答返回（本机到 DNS 服务器的查询过程称为递归：一路查下去中间不返回，直到得到最终结果）
  - DNS 服务器：根服务器收到请求后，并不解析地址，而是将顶级域 DNS 服务器的 IP 返回给本地 DNS 服务器，让它重新发送请求。同理，顶级域 DNS 服务器在查询完缓存后会将二级域名服务器的地址返回。（DNS 服务器到 DNS 服务器的查询过程称为迭代：查到一个可能查到一个可能直到的服务器地址就将此地址返回，重新发送解析请求）

### 建立 TCP 连接

连接步骤：

0. 服务端打开 socket 开始监听连接（被动打开）
  - 进入 LISTEN 状态
1. 客户端向服务端发送一个 SYN 请求报文来主动打开连接
  - 进入 SYN_SEND 状态
  - 序号为随机设定的 A
  - SYN 报文段不带数据，但是会消耗一个序号
2. 服务端收到报文后，如果同意建立连接则返回一个 SYN/ACK 确认报文
  - 进入 SYN_RCVD 状态
  - 序号为随机设定的 B，确认序号为 A+1
3. 客户端收到报文后，返回一个 ACK 确认报文
  - 进入 ESTABLISHED 状态
  - 序号为 A+1，确认序号为 B+1（因为被 SYN 消耗了一个序号所以最终都加了 1）
  - 这个时候一般会携带真正需要传输的数据，如果服务器也收到该数据报文就会同样进入 ESTABLISHED 状态，此时 TCP 连接建立完成

确定超时总共需要：1 + 2 + 4 + 8 + 16 + 32 = 63s

### TLS 握手

HTTPS 在 HTTP 的基础上加了一层 SSL/TLS，增加了身份验证和加密信息。

SSL：Secure Sockets Layer，安全套接层

TLS：Transport Layer Security， 传输层安全协议

过程：

1. TCP 连接建立后，客户端发送一个随机值，需要的协议和加密方式
2. 服务端收到客户端的随机值，自己也产生一个随机值，并根据客户端需求的协议和加密方式来使用对应的方式，发送自己的证书（如果需要验证客户端证书需要说明）
3. 客户端收到服务端的证书并验证（通过证书关系链从根 CA 验证）是否有效，验证通过会再生成一个随机值，通过服务端证书的公钥去加密这个随机值并发送给服务端，如果服务端需要验证客户端证书的话会附带证书
4. 服务端收到加密过的随机值并使用私钥解密获得第三个随机值，这时候两端都拥有了三个随机值，可以通过这三个随机值按照之前约定的加密方式生成密钥，接下来的通信就可以通过该密钥来加密解密

在 TLS 握手阶段，两端使用非对称加密的方式来通信，但是因为非对称加密损耗的性能比对称加密大，所以在正式传输数据时，两端使用对称加密的方式通信。

### 获得页面响应

重定向响应：如果服务器返回了跳转重定向（非缓存重定向），那么浏览器端就会向新的URL地址重新走一遍DNS解析和建立连接。

200：浏览器开始解析页面，进入准备渲染的阶段。

### 页面资源加载

浏览器在解析页面的过程中会去请求页面中诸如 js、css、img 等外联资源，这些请求也是要建立连接的。

优化：启用长连接，同一客户端 socket 针对同一 socket 后续请求都会复用一个 TCP 连接进行传输；针对 SSL/TLS 握手有会话恢复机制，验证通过后，可以直接使用之前的对话密钥，减少握手往返。

## 工作流程

[render process](https://coolshell.cn/wp-content/uploads/2013/05/Render-Process-768x250.jpg)

主流程：

1. 解析（parse、construct）
  - HTML/SVG/XHTML：构建 DOM tree
  - CSS：构建 CSS Rule Tree
  - JS：通过 api 来操作 DOM Tree 和 CSS Rule Tree 
2. 呈现（render）：通过 DOM Tree 和 CSS Rule Tree 来构建 Rendering Tree
  - 包含多个带有视觉属性的矩形
  - Rendering Tree 并不等同于 DOM Tree
  - Rendering Tree 上的每个节点会被附加 CSS Rule
3. 布局（layout/reflow）：计算每个节点的位置
4. 绘制（paint）：遍历呈现树，并由后端层将每个节点绘制出来

阻塞关系：

- js 在执行的时候会阻塞 DOM 解析（但是还是可以下载其他资源）
- 没有 defer async 的 js 标签在下载的时候也会阻塞 DOM 解析
- CSS 的解析不会阻塞 DOM 的解析，但是会阻塞 DOM 的渲染（因为需要合并成 Rendering Tree 之后渲染）
- 但是 CSS 的解析会阻塞 js 执行（加载不会），而 js 又会阻止其后的 DOM 解析

### DOM 解析

[构建对象模型](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/constructing-the-object-model?hl=zh-cn)

将文档解析成代表文档结构的节点树（解析树或者语法树）

Bytes → characters → tokens → nodes → object model

### CSS 解析

通过与 DOM 解析相同的步骤获得 CSSOM

由于构建 CSSOM 树是一个十分消耗性能的过程，所以应该尽量保证层级扁平，减少过度层叠，越是具体的 CSS 选择器，执行速度越慢。

### render -> layout -> paint

[render tree construction](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/render-tree-construction?hl=zh-cn)

1. 构建 Rendering Tree
  - 遍历 DOM 树的每个可见节点
  - 为其找到适配的 CSSOM 规则并应用它们
  - 发射可见节点，连同其内容和样式
2. 布局
  - 从渲染树的根节点开始遍历
  - 输出多个盒模型（精确地捕获每个元素在视口内的确切位置和尺寸：几何信息）
3. 绘制（栅格化）
  - 根据节点的计算样式和几何信息将其转换成屏幕上的实际像素

### 渲染层合并 composite

对页面中 DOM 元素的绘制是在多个层上进行的。在每个层上完成绘制过程之后，浏览器会将所有层按照合理的顺序合并成一个图层，然后显示在屏幕上

## cookie & session

HTTP 本身是无状态的，需要记录用户状态时就会用到 cookie 和 session。

### cookie

服务器发送到用户浏览器并保存在本地的一小块数据，它会在浏览器下次向同一服务器再发起请求时被携带并发送到服务器上。

一般用于：

1. 会话状态管理（如用户登录状态、购物车、游戏分数或其它需要记录的信息）
2. 个性化设置（如用户自定义设置、主题等）
3. 浏览器行为跟踪（如跟踪分析用户行为等）

缺点：会在用户访问服务器的时候被带上，增大请求包大小。

如何创建：当服务器收到 HTTP 请求时，服务器可以在响应头里面添加一个 Set-Cookie 选项。浏览器收到响应后通常会保存下 Cookie。

保存时间：没有设置 Expires 或者 Max-Age 的话，在会话结束（浏览器关闭）时就会被删除。而设置了则可以给 Cookie 指定一个过期时间（客户端时间） / 有效期。

安全：设置为 Secure 的 Cookie 只应通过被 HTTPS 协议加密过的请求发送给服务端。设置为 HttpOnly 的 Cookie 将无法被 Document.cookie 访问（避免 XSS）。

作用域：Domain 标识指定了哪些主机可以接受 Cookie 。如果不指定，默认为当前文档的主机（不包含子域名）。如果指定了 Domain ，则一般包含子域名。Path 标识指定了主机下的哪些路径可以接受 Cookie。

SameSite Cookie

### session

保存在服务端的一种数据结构，用来跟踪用户状态。每个用户都会有一个 session id，对应于存在服务器上的值。

第一次创建 Session 的时候，服务端会在 HTTP 协议中告诉客户端，需要在 Cookie 里面记录一个 session id，以后每次请求把这个会话 ID 发送到服务器，就可以找到对应的 session 了。如果浏览器禁用 cookie 就会把 sid 加到 URL 上。

## 存储

cookie：一般由服务器生成，可设置过期时间，4kb，每次请求都会携带在header中

sessionStorage：页面关闭时就清理，5mb

localStorage：没有过期时间，需要手动清理，5mb

indexedDB：没有过期时间，需要手动清理，大小无限

Service Worker：用来做缓存文件，提高首屏速度

## 跨域

协议、域名、端口有一个不同就是跨域。

1. iframe
2. 服务器代理
3. postMessage
4. JSONP
5. document.domain
6. CORS

### postMessage

通常用于获取嵌入页面中的第三方页面数据：iframe，一个页面发送消息，另一个页面判断来源并接收消息。

```js
// 发送消息端
window.parent.postMessage('message', 'http://test.com');
// 接收消息端
var mc = new MessageChannel();
mc.addEventListener('message', (event) => {
  var origin = event.origin || event.originalEvent.origin; 
  if (origin === 'http://test.com') {
    console.log('验证通过')
  }
});
```

### JSONP

缺点是只能发送 get 请求，而且需要服务端配置。

```js
// 简单版
function jsonp (url, jsonpCallback, success) {
  let script = document.createElement("script");
  script.src = url + '?callback=' + jsonpCallback;
  script.async = true;
  script.type = "text/javascript";
  window[jsonpCallback] = function(data) {
    success && success(data);
  };
  document.body.appendChild(script);
  // 错误处理
  script.onerror = () => {
    console.log('err')
  }
}

// 复杂版
(function(global) {
  function generateJsonpCallback() {
    return `jsonpCallback_${Date.now()}_${Math.floor(Math.random() * 10000)}`
  }

  function removeScript(id) {
    document.body.removeChild(document.getElementById(id))
  }

  function removeFunc(name) {
    delete global[name]
  }

  function jsonp(url, success, error, timeout = 3000) {
    let timer
    const funcName = generateJsonpCallback()
    const script = document.createElement("script")
    script.src = `${url}?callback=${funcName}`
    script.id = funcName
    script.async = true
    script.type = "text/javascript"
    document.body.appendChild(script)

    global[funcName] = res => {
      success && success(res)
      timer = setTimeout(() => {
        removeScript(funcName)
        removeFunc(funcName)
      }, timeout)
    }

    script.onerror = () => {
      error && error()
      removeScript(funcName)
      removeFunc(funcName)
      if (timer) clearTimeout(timer)
    }
  }

  window.jsonp = jsonp
})(window)
```

清除工作：

在 onload/complete 上删除节点（或者用定时器，在成功回调上删除？），有必要的话还要用 for-in 遍历节点来删除其属性（比如 src）。

服务端：

返回 callback + '(' + JSON.Stringify(data) + ')'

### document.domain

用于在两个地址的二级域名相同的情况下实现跨域，会共用 cookie。

顶级、一级域名：.com
二级域名：zhihu

### CORS

跨域资源共享，是一个 W3C 标准，浏览器一旦发现 AJAX 请求跨域，就会自动添加一些附加的头信息，只要服务器也实现了 CORS 接口，就可以实现跨域通信。

对于简单请求，浏览器直接发出 CORS 请求，只在头信息中添加一个 Origin 字段，表示请求的源（协议 + 域名 + 端口）。

如果不是简单请求就会发送预检请求，包含 Origin, Access-Control-Request-Method, Access-Control-Request-Headers 字段。

- 预检请求
- 过期时间
- 允许的头
- 允许的请求方法

## location

- hash：#右边的内容
  - onhashchange
  - hash 改变时前进后退按钮也会有记录
  - hash 部分不会发往服务器
- host：ip + port
- hostname：ip
- href：total
- origin：protocol + host
- pathname：第一个 / 后面的内容到 ? 为止
- port：port
- protocol：protocol
- search：? 后面的内容到 # 为止
- assign()
- replace()

## XSS

跨站脚本攻击，属于代码注入的一种，攻击者通过拼接js将代码注入网页中，对网页进行篡改，用户访问时就会受代码影响，暴露隐私数据或者发起 CSRF 攻击。利用的是用户对指定网站的信任。

防御方法：

1. 过滤特殊字符 < > script，或者对用户输入进行URL编码
2. 设置cookie时加上HttpOnly参数，能阻止cookie被获取
3. 不要使用innerHTML
4. 通过 Content-Security-Policy Header 开启 CSP，规定了浏览器只能够执行特定来源的代码 Content-Security-Policy: default-src 'self'

### CSP

Content-Security-Policy，HTTP 头，也可以通过页面的 meta 标签来配置，用来限制页面的行为：

- 能（否）使用某个域的资源
- 能（否）连接某个服务器
- 能否被放入iframe，能否在其内放入iframe
- 能否使用内联样式/脚本

CSP 通过指定有效域——即浏览器认可的可执行脚本的有效来源——使服务器管理者有能力减少或消除 XSS 攻击所依赖的载体。一个 CSP 兼容的浏览器将会仅执行从白名单域获取到的脚本文件，忽略所有的其他域的脚本。

```html
<meta http-equiv="Content-Security-Policy" content="default-src 'self'; img-src https://*; child-src 'none';">

服务端配置：Content-Security-Policy: default-src 'self'; img-src *; media-src media1.com media2.com; script-src userscripts.example.com
```

## CSRF

跨站请求伪造，借助用户的cookie，让用户发起请求，完成一些违背用户行为的操作（预先构造好请求参数或者直接伪造用户身份），一般通过XSS，命令行伪造实现，或者让用户在访问黑客编写的网站时提交请求。实际上利用的是网站对用户的信任。

防御方法：

1. 验证 HTTP Referer 字段：但是有些用户设置浏览器不提供 Referer，并且低版本浏览器的 Referer 可以伪造
2. 在请求地址中添加 token 并验证：问题是每个请求都要设置携带 token 太麻烦，而且如果在网站中访问到恶意链接（通过XSS），也会携带上 token
3. 在 HTTP 头中自定义属性并验证：在 XMLHttpRequest 中添加 添加 X-CSRFToken Header，问题是只适用于 XHR 请求
4. 重要的 cookie 存储时间要尽量短，并且重要操作执行前必须要确认

# 网络
## HTTP
### 状态码

200 & OK: 请求成功；

204 & No Content: 请求处理成功，但没有资源可以返回；

206 & Partial Content: 对资源某一部分进行请求(比如对于只加载了一般的图片剩余部分的请求)；

301 & Move Permanently: 永久性重定向；

302 & Found： 临时性重定向；

303 & See Other: 请求资源存在另一个URI，应使用get方法请求；

304 & Not Modified: 服务器判断本地缓存未更新，可以直接使用本地的缓存；

307 & Temporary Redirect: 临时重定向；

400 & Bad Request: 请求报文存在语法错误；

401 & Unauthorized: 请求需要通过HTTP认证；

403 & Forbidden: 请求资源被服务器拒绝，访问权限的问题；

404 & Not Found: 服务器上没有请求的资源；

500 & Internal Server Error: 服务器执行请求时出现错误；

502 & Bad Gateway: 错误的网关；

503 & Service Unavailable: 服务器超载或正在维护，无法处理请求；

504 & Gateway timeout: 网关超时；

# Vue

[知识点](https://github.com/tangxiangmin/interview/blob/master/%E7%9F%A5%E8%AF%86%E7%82%B9/Vue.md)
[面试题](https://juejin.im/post/59ffb4b66fb9a04512385402)
[面试题](https://www.jianshu.com/p/e54a9a34a773)

## DOM 更新是异步的

只要观察到数据变化，Vue 将开启一个队列（watcher queue），并缓冲在同一事件循环中发生的所有数据改变。如果同一个 watcher 被多次触发，只会被推入到队列中一次。

然后，在下一个的事件循环“tick”中，Vue 刷新队列并执行实际 (已去重的) 工作（watcher 的 run）。Vue 在内部尝试对异步队列使用原生的 Promise.then 和 MessageChannel，如果执行环境不支持，会采用 setTimeout(fn, 0) 代替。

所以想要在数据变化之后马上进行 DOM 操作并能获取到数据，需要将其写在 vm.$nextTick 的回调函数中。

## computed 和 watch 的区别

watch：检测到某一属性的变化后执行相应的回调。

computed：计算属性结果依赖的值变化之后改变计算属性的值。

## 数据绑定

实现双向绑定需要：defineProperty 劫持数据变化，给属性添加发布订阅来通知视图更新。

### observe

遍历 data 属性，给每个属性调用 observeProperty。

observeProperty 创建 Dep 实例，observe 属性的值（遍历子属性），给属性添加 getter 和 setter。

getter 中往 dep 里添加 watcher：创建 watcher 实例的时候将 Dep.target 设为这个 watcher，之后获取依赖的值，触发依赖的 getter，在其中将 Dep.target 添加到其 dep 中。

setter 设置新值并让 dep 通知 watcher 数据变更。

### Dep

Dep.target：watcher 实例。

addsub：将订阅者 watcher 添加到 this.subs 中。

notify：通知 this.subs 中的订阅者数据变更（调用 sub.update()）。

### Watcher

在 constructor 中将 Dep.target 指向自己，之后触发属性的 getter，再清空 Dep.target。

update：调用传入的 cb 进行视图更新。

## 生命周期

- init Events & Lifecycle
- * beforeCreate
- init Injections & reactivity
- * created
- 没有 el 选项的话就等待 vm.$mount(el) 调用，然后进入下一步
- 判断是否有 template
    - 没有的话将 el 的 outerHTML 作为 template
    - 有的话将  template 编译成 render 函数
- * beforeMount
- 创建 vm.$el，并把渲染内容挂载到 DOM 上
- * mounted
- * beforeUpdate
- Virtual DOM re-render and patch
- * updated
- * beforeDestroy
- teardom watchers, child components and event listeners
- * destroyed

more：

- activated：keep-alive 组件激活时调用
- deactivated：keep-alive 组件停用时调用
- errorCaptured：当捕获一个来自子孙组件的错误时被调用

### 触发时机

- 初始化组件时仅执行 beforeCreate/Created/beforeMount/mounted 四个钩子函数
- 当改变 data 中的变量时会执行 beforeUpdate/updated 钩子函数

### 父子组件的生命周期

- 初始化时，父组件初始化到 beforeMount 阶段后开始初始化子组件到挂载为止，之后才挂载父组件，并且会主动触发一次更新
- data 改变时各自更新
- props 改变时：p-beforeUpdate -> c-beforeUpdate -> c-updated -> p-updated
- 父组件摧毁时：p-beforeDestroy -> c-beforeDestroy -> c-destroyed -> p-destroyed

## 组件通信
### 父 -> 子

- props
- $children：当前实例的直接子组件，但是并不保证顺序，而且也不是响应式的

### 子 -> 父

- 事件：$emit('func', data)（用第二个参数传参） + @func="handleMsg"
- $parent

### 兄弟

- 使用一个 Vue 实例作为中央事件总线，之后使用 $on $emit 通信

### 多层级父子组件

- $broadcast & $dispatch？（1.0）
    - 作为替代可以手动实现
    - 或者实现一个简易的 vuex 来通信
- provide / inject （2.2+）
    - 适合组件库或插件

### 复杂的单页应用数据管理

vuex

## provide/inject

这对选项需要一起使用，以允许一个祖先组件向其所有子孙后代注入一个依赖，不论组件层次有多深，并在起上下游关系成立的时间里始终生效，类似 React 的 context。

主要为高阶插件/组件库提供用例。

## 自定义组件指令

```js
Vue.directive('my-directive', {
  bind: function (el, binding) {}, // 指定第一次绑定到元素时调用，可进行初始化设置
  inserted: function (el, binding) {}, // 插入父节点时调用
  update: function (el, binding) {}, // 所在组件的 VNode 更新时调用，但是可能发生在其子 VNode 更新之前
  componentUpdated: function (el, binding) {}, // 指令所在组件的 VNode 及其子 VNode 全部更新后调用
  unbind: function (el, binding) {} // 指令与元素解绑时调用
})
```

# React
## setState

每次 setState 都会导致 re-render，即使没有改变任何值，这是因为 shouldComponentUpdate 默认返回 true。

setState 在 React 中是异步的，它会把多次的状态更新整合为一次（相当于 shallow merge），对于同一个状态的多次更新只会执行最后一次。而异步和 DOM 事件是同步的（使用了 React Fiber 后是异步的）。

想要同步调用，就需要传入函数。因为对象会被 shallow merge，但是函数只会被 queue 起来，下一个函数接收到的 state 都是前一个函数操作后的 state。另一种方法是用第二个参数来执行回调。

```js
this.setState(prev => ({ counter: prev.counter + 1 }))
```

### 异步更新的原理（Batch Update）

Batch Update 对于使用 virtual dom 来更新 view 的 MV* 框架来说很重要，可以合并更新，减少 virtual dom 的更新次数。

是否合并的判断：若 batchingStrategy.isBatchingUpdates 为 true 则将调用了 setState 的组件放入 dirtyComponents 数组中，为 false 则调用 batchedUpdates 方法更新队列中的内容。

batchedUpdates：将 isBatchingUpdates 设为 true，表示正在更新中，之后调用 transaction.perform。

Transaction：它会给函数包装一层 Warpper，在 perform 执行前后额外执行 所有 warpper 的 initialize 和 close 方法。

在 Transaction 的 initialize 阶段，一个 update queue 被创建。在 Transaction 中调用 setState 方法时，状态并不会立即应用，而是被推入到 update queue 中。函数执行结束进入 Transaction 的 close 阶段，update queue 会被 flush，这时新的状态会被应用到组件上并开始后续 Virtual DOM 更新等工作。

比如默认第一次应用初始化的时候是一次事务的进行，在用户交互的时候是一次新的事务开始，会在同一次同步事务中标记 batchUpdate=true，这样的做法是不破坏使用者的代码。

然后如果是 Ajax，setTimeout 等要离开主线程进行异步操作的时候会脱离当前 UI 的事务，这时候再进入此次处理的时候 batchUpdate=false，所以才会 setState 几次就 render 几次。

Vue 的 Batch Update 直接利用了 Event Loop 来实现。

## ref

适合使用 ref 的情况：

- 处理焦点、文本选择或媒体控制。
- 触发强制动画。
- 集成第三方 DOM 库

创建：React.createRef()

当 ref 属性被用于一个普通的 HTML 元素时，React.createRef() 将接收底层 DOM 元素作为它的 current 属性以创建 ref 。

当 ref 属性被用于一个自定义类组件时，ref 对象将接收该组件已挂载的实例作为它的 current 。

函数式组件不能创建因为没有实例，但是内部还是可以使用的，只要它指向一个 DOM 元素或者 class 组件。

```jsx
<input
  type="text"
  ref={(input) => { textInput = input; }} 
/>
```

## 生命周期
### old

挂载：

constructor -> componentWillMount -> render -> componentDidMount

更新：

props change -> componentWillReceiveProps -> shouldComponentUpdate -> componentWillUpdate -> render -> componentDidUpdate

state change -> shouldComponentUpdate -> componentWillUpdate -> render -> componentDidUpdate

卸载：

componentWillUnmount

### new

挂载：

constructor -> getDerivedStateFromProps -> render -> componentDidMount

更新：

props or state change -> getDerivedStateFromProps -> shouldComponentUpdate -> render -> getSnapshotBeforeUpdate -> componentDidUpdate

卸载：

componentWillUnmount

### 可打断的生命周期

componentWillMount, 
componentWillReceiveProps, 
shouldComponentUpdate, 
componentWillUpdate, 
是可以被打断的，这样可能会多次调用这些生命周期函数（开启 async rendering 的时候），所以在 v16 中添加了新的 API，并预备移除则三个不安全的旧生命周期（除了 shouldComponentUpdate）。

使用 static getDerivedStateFromProps 替代 componentWillReceiveProps，该函数会在初始化和 update 时调用（props 或 state 更新），另外需要注意的是不能使用 this。

getSnapshotBeforeUpdate 在 render 之后，DOm 渲染之前调用，componentWillUpdate 在 render 之前调用。

### 用法建议

```js
class ExampleComponent extends React.Component {
  // 用于初始化 state
  constructor() {}
  // 用于替换 `componentWillReceiveProps` ，该函数会在初始化和 `update` 时被调用
  // 因为该函数是静态函数，所以取不到 `this`
  // 如果需要对比 `prevProps` 需要单独在 `state` 中维护
  static getDerivedStateFromProps(nextProps, prevState) {}
  // 判断是否需要更新组件，多用于组件性能优化
  shouldComponentUpdate(nextProps, nextState) {}
  // 组件挂载后调用
  // 可以在该函数中进行请求或者订阅（添加事件）
  componentDidMount() {}
  // 用于获得最新的 DOM 数据
  getSnapshotBeforeUpdate() {}
  // 组件即将销毁
  // 可以在此处移除订阅，定时器等等
  componentWillUnmount() {}
  // 组件销毁后调用
  componentDidUnMount() {}
  // 组件更新后调用
  componentDidUpdate() {}
  // 渲染组件函数
  render() {}
  // 以下函数不建议使用
  UNSAFE_componentWillMount() {}
  UNSAFE_componentWillUpdate(nextProps, nextState) {}
  UNSAFE_componentWillReceiveProps(nextProps) {}
}
```

## React Fiber

将 js 中的同步更新过程进行分片，每执行完一段更新过程，就把控制权交还给React负责任务协调的模块，看看有没有其他紧急任务要做，如果没有就继续去更新。

这样就会导致低优先级的更新过程可能会被高优先级的更新过程打断，其工作作废并重新开始。

Reconciliation 阶段的生命周期（will、should）会被打断，如果正好执行到一半就需要重新调用。Commit 阶段的生命周期则会直接更新完 DOM。

# node
## node 事件循环

Node 的 Event Loop 分为 6 个阶段，它们会按照顺序反复执行。每个阶段都有一个 FIFO 队列来执行回调，队列变空或达到限制时进入下一个阶段。

macrotask 会在各自的阶段执行，而 microtask 则会在当前操作完成后立即执行，Node 的 process.nextTick 优先于其他 microtask 执行。

### timer

执行已安排的 setTimeout 和 setInterval 的回调函数。

一个 timer 指定的时间并不是准确时间，而是在达到这个时间后尽快执行回调，可能会因为系统正在执行别的回调而延迟。

下限的时间有一个范围：[1, 2147483647] ，如果设定的时间不在这个范围，将被设置为1。

### I/O（pending callbakcs）

执行除了 close 事件，timer 和 setImmediate 的回调函数（I/O 回调）。

轮询阶段达到限制或者不处于轮询阶段时需要加入的 I/O 回调会加入到这里。

### idle, prepare

仅系统内部使用。

### poll（轮询）

如果其他地方没有代码要执行的话会一直停在这个阶段，等待新的 I/O 事件（waiting for an incoming connection. request, etc）。

但是也不能让 poll 一直执行回调函数从而无法进入其他阶段，libuv 会给 poll 设置一个执行函数数目的限制，让它停止 polling for more events。

进入 poll 阶段时有定时器：

1. Calculating how long it should block and poll for I/O, then
2. Processing events in the poll queue.

进入 poll 阶段时没有定时器：

- 如果 poll 队列不为空，就遍历队列同步执行其中的回调，直到队列为空或达到系统限制（libuv）
- 如果 poll 队列为空
  - 如果有 setImmediate 需要执行，poll 阶段会停止并且进入到 check 阶段执行 setImmediate
  - 如果没有则会停在 poll 阶段，等待回调函数加入队列并马上执行它

如果有的定时器需要被执行，会回到 timer 阶段执行回调。

### check

执行 setImmediate 的回调函数。

poll 阶段队列变空且有 setImmediate 在队列中时会进入这个阶段。

### close callbacks

执行 close 事件的回调函数。

# Git

- git log：最近的 commit 记录
  - git log --pretty=oneline：输出 commit 的 commit id
- git reset：版本回退
  - git reset --hard HEAD^：回退到上一个版本
  - git reset --hard HEAD~100
  - git reset --hard 1094a（commit id）
- HEAD：其实是内部指向当前版本的指针
- git reflog：记录了每一个命令（可以用于恢复新版本）
- git checkout --file：回退到文件最近的一次 commit 或 add，不带 -- 的话就是切换分支
- git reset HEAD <file>：可以把暂存区的修改撤销（撤销 add）
- git checkout -b dev：切换分支到 dev，-b 相当于 git branch dev，创建 dev 分支
- git branch：列出所有分支
- git branch -d dev：删除 dev 分支
- git merge dev：合并指定分支到当前分支
- git log --graph
- git merge --no-ff：merge 后会生成一个新的 commit，不会丢失被合并的分支信息
- git stash：保存暂时不想 commit 的工作现场
- git stash apply + git stash drop === git stash pop
- git rebase：把分支提交历史整理成直线，缺点是可能需要解决多次冲突（merge 只用解决一次）
- git tag v1.0

# Webpack

[知识点](https://github.com/tangxiangmin/interview/blob/master/%E7%9F%A5%E8%AF%86%E7%82%B9/webpack.md)


# interview
## 进程和线程的区别

进程是资源分配的最小单位，线程是 CPU 调用的最小单位。

线程的细粒度要比进程小一点，比如 Node 进程里，js 在其中一个线程执行，大部分 I/O 操作在另一个线程执行。

线程之间是默认是可以共享内存的，进程一般来说是不共享内存的。

## 浏览器缓存机制

分为强缓存和协商缓存两种。

强缓存：Expires、Cache-Control: max-age，在缓存期间不需要请求，返回 200。

协商缓存：如果缓存过期了就需要协商缓存，需要请求，如果缓存有效返回 304，否则返回新的资源（200）。

Expires：HTTP 响应头字段，用于告诉浏览器在过期时间前可以直接从浏览器缓存中获取数据（HTTP 1.0）

Cache-Control：max-age，与 Expires 类似，都是指明当前资源的有效期，但是功能会更细致一些，优先级高于 Expires（HTTP 1.1）

Last-Modified / If-Modified-Since：配合 Cache-Control 一起使用

Last-Modified Header：Server 在返回响应的时候，可以加上，表示资源上一次更改的时间

If-Modified-Since：当请求的资源快过期（max-age）时，如果 response 里有 Last-Modified ，再次发送请求的时候可以带上 If-Modified-Since ；之后由服务器比对两者的时间，若最后修改时间更新则说明资源有改动，则响应整个资源内容（200）；更旧则继续使用缓存，并响应 304

ETag / If-None-Match：主要解决了几个 Last-Modified 的问题：

1. Last-Modified标注的最后修改只能精确到秒级，如果某些文件在1秒钟以内，被修改多次的话，它将不能准确标注文件的修改时间
2. 如果某些文件会被定期生成，但有时内容并没有任何变化（或因为其他操作只修改了编辑时间，没有修改内容）， Last-Modified 却会改变，导致文件没法使用缓存
3. 有可能存在服务器没有准确获取文件修改时间，或者与代理服务器时间不一致等情形

Etag 是服务器自动生成或者由开发者生成的对应资源在服务器端的唯一标识符，表示文档内容是否改动（类似 hash?），Server 在 response 里带上 Etag ，优先级会高于 Last-Modified

而浏览器则会根据 Etag 在再次发送请求的时候带上 If-None-Match 询问是否有新资料（传的就是 Etag），没有的话返回 304

私有缓存：Cache-Control: private
表示该响应是专用于某单个用户的，中间人不能缓存此响应，该响应只能应用于浏览器私有缓存中

Vary 响应头：User-Agent
对于后续（缓存之后）的请求头，只有当前的请求和原始（缓存）的请求头跟缓存的响应头里的 Vary 都匹配，才能使用缓存

不缓存的情况：Cache-Control: no-store, no-cache

1. 不缓存：指定 max-age=0 或者 Cache-Control: no-store（每次访问都请求资源）

2. 需要缓存但是更新时能马上显示：
设置 max-age=0 以及添加上述的 Last-Modified 或 Etag 头应该就可以，另一种方案是使用 Cache-Control: no-cache （与 no-store 有点不同，每次访问都需要发送请求确定是否有新资源，没更新依旧返回 304）

3. 如果需要不发送请求（或者尽量少发），但依旧能使用缓存，并在更新时立马获取新资源：
- 对于频繁更新的资源，设置一个尽量长（低于更新间隔）的 max-age 即可
- 在资源的 URL 后面（通常是文件名后面）加上版本号，并设置一年及更长的过期时间，当资源更新时只用在高频变动的资源文件（html）里做入口的改动

# todo
## 缓存

## redux

## WebSocket

解决了 HTTP 服务度不能主动推送到客户端的问题。

- 支持双向通信
- 较少的控制开销。连接创建后，ws客户端、服务端进行数据交换时，协议控制的数据包头部较小。在不包含头部的情况下，服务端到客户端的包头只有2~10字节（取决于数据包长度），客户端到服务端的的话，需要加上额外的4字节的掩码。而HTTP协议每次通信都需要携带完整的头部

## spa pwa ssr

## MVVM MVC

## Web Worker

## promise.all() 、promise.race()

## 数组展平

[].concat(...a)

## node http

## HOC

一个函数接收一个组件类返回一新的组件类。

## 调用栈

用来存储方法调用和基础数据类型的地方。

由于函数需要在执行结束后跳转回调用该函数的代码位置，因此计算机必须记住函数调用的上下文，调用栈就是存储这个上下文的区域。每当函数调用时，当前的上下文信息会被存储在栈顶，函数返回时系统会删除在栈顶的上下文信息，并使用该信息继续执行程序。递归过多会导致栈存储空间过大（栈空间溢出）。

## 不带 referer

```html
<meta name="referrer" content="no-referer">
<img src="xxxx.jpg"  referrerPolicy="no-referrer">
```

## 请求头和响应头

```
GET /a/b.html?a=b&c=d HTTP/1.1
Host: www.example.com
User-Agent: ghjghjk

HTTP/1.1 200 OK
Date:
Content-Length:
Content-Type: 
```

## SQL 注入

## 垃圾回收

(click me)[https://yuchengkai.cn/docs/zh/frontend/#v8-%E4%B8%8B%E7%9A%84%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6%E6%9C%BA%E5%88%B6]

## Promise
### Promise.all

`Promise.all(iterable)`

返回一个 promise 实例，在参数 iterable 内的所有 promise 都完成或不包含 promise 时 resolved，如果参数中某一个 promise 失败了，则 rejected，失败原因是第一个失败的 promise 的结果。

```js
const [a, b] = await Promise.all([get(url), get(url2)]) // 同时获取
```

### Promise.race

`Promise.race(iterable)`

返回一个 promise 实例，由 iterable 中的第一个完成或失败的 promise 决定结果。

## DataURL

前缀为 data: 协议的的 URL，其允许内容创建者向文档中嵌入小文件。

优势：

- 不用发送 HTTP 请求，适合小资源

劣势：

- 客户端需要解码。且它解码过程在移动端消耗稍多
- 编码后的尺寸是之前的130%
- 无法缓存

ObjectURL：

一种用于展示 File object 或 Blob object 的 URL。

```js
var objectURL = URL.createObjectURL(object);
var img = document.createElement('img')
img.src = objectURL
```

## diff

由于 DOM 是多叉树结构，完成地比较差异的话需要的时间复杂度会是 O(n^3)，所以 React 团队做出假设，只在同层比较，这样就实现了 O(n) 复杂度的 diff 算法。

## 设计模式

## koa中间件实现原理

## react 性能优化

## 如何用 js 实现动画

## css 动画以及与 js 动画性能比较

## 模板引擎实现原理

用特定的dls表达出一段内容中的特定部分需要以各种形式被插入不同的数据，
模板引擎往往会先模板字符串编译为模板函数（为的是后续不需要每次都解析模板字符串），
之后再调用模板函数并传入数据，得到模板被数据插值之后的内容。

## 什么是高阶组件

## http 2.0 的特性

https://yuchengkai.cn/docs/zh/cs/#http-2-0

## 简述你对tcp协议udp协议及http协议的理解，并解释tcp与http的区别

```
tcp：
  基于连接。保证数据按顺送达。
  服务器监听在某个端口，客户主动发起连接。
  连接建立完成，即成为一个字节流的双向管道。

udp：
  基于消息，无连接，不保证送达。
  任何时候任意两个udp端口之间都可以相互通信。
  通信以单个消息为单位。

http:
  基于tcp的请求响应模型：
    建立tcp连接，客户端发送请求，服务器返回响应。tcp连接断开。

tcp与http的区别：
  http整个报文（包括请求头和请求体）都是tcp的payload（载荷）
  http的payload则是http自身的请求/响应体。
  http是只能在tcp上发送http格式的报文。
```

## tracert

此命令可以得出ip数据包到达目的地址所经过的中间路由。

其原理是借助ip协议的ttl字段及ip数据包的ttl被减为0以后，那个路由会告诉源地址，因为ttl减为0导致包不可达。反馈的协议是ICMP。

实际应用中，一般会发送tcp握手包。

## Stream

处理流式数据的接口，采用数据块（chunk）的方式读取数据，每次只读入数据的一小块，然后触发事件。

分为可读流（Readable）、可写流（Writable）、可读写流（Duplex）、在读写过程中可以修改或转换数据的 Duplex 流（Transform）四种，所有流都是 EventEmitter 的实例。

各部分通过 pipe 连接。

Node 中有很多流对象，像 HTTP 请求、socket 流、process.stdout 等都是流的实例。

读写数据时，每读入（或写入）一段数据，就会触发一次 data 事件，全部读取（或写入）完毕，触发 end 事件。如果发生错误，则触发 error 事件。

生产者消费者问题：

生产者的主要作用是生成一定量的数据放到缓冲区中，然后重复此过程。与此同时，消费者也在缓冲区消耗这些数据。该问题的关键就是要保证生产者不会在缓冲区满时加入数据，消费者也不会在缓冲区中空时消耗数据。

### Readable

可写流有两种模式，流动模式（flow mode）和暂停模式（pause mode）。

流动模式：

在 Stream 上绑定 ondata 方法就会自动触发这个模式，系统自行调用读取数据。

(refer)[https://www.barretlee.com/blog/2017/06/06/dive-to-nodejs-at-stream-module/]

资源的数据流并不是直接流向消费者，而是先 push 到缓存池，缓存池有一个水位标记 highWatermark，超过这个标记阈值，push 的时候会返回 false，什么场景下会出现这种情况呢？

- 消费者主动执行了 .pause()
- 消费速度比数据 push 到缓存池的生产速度慢

有个专有名词来形成这种情况，叫做「背压」，Writable Stream 也存在类似的情况。

暂停模式：

这是 Stream 的预设模式，当监听 readable 时就是这种模式，需要手动调用 read 方法来读取数据。

资源池会不断地往缓存池输送数据，直到 highWaterMark 阈值，消费者监听了 readable 事件并不会消费数据，需要主动调用 .read([size]) 函数才会从缓存池取出，并且可以带上 size 参数，用多少就取多少。

可以通过 _readableState.buffer 来查看缓存池缓存了多少资源。

### Writable

数据流过来的时候，会直接写入到资源池，当写入速度比较缓慢或者写入暂停时，数据流会进入队列池缓存起来。

当生产者写入速度过快，把队列池装满了之后，就会出现「背压」，这个时候是需要告诉生产者暂停生产的，当队列释放之后，Writable Stream 会给生产者发送一个 drain 消息，让它恢复生产。

### Duplex

继承了 Readable Stream，并拥有 Writable Stream 的方法。

### Transform

### pipe

```js
// 伪代码
Readable.prototype.pipe = function(writable, options) {
  this.on('data', (chunk) => {
    let ok = writable.write(chunk);
    // 消费过慢发生背压，暂停
    !ok && this.pause();
  });
  writable.on('drain', () => {
    // 消费者完成消费，恢复
    this.resume();
  });
  // 告诉 writable 有流要导入
  writable.emit('pipe', this);
  // 支持链式调用
  return writable;
};
```

### 事件

- stream.on('data')：数据流收到数据
- stream.on('readable)：数据流向外提供数据
- stream.on('drain')：恢复 writer

## Process

- process.cwd()：获取当前工作目录
- process.env：获取环境变量配置
- process.nextTick：在每个 Event Loop 阶段结束后执行 nextTickQueue 中的 'Tick'

### 标准流

由于 process.stdin 和 process.stdout 都是流形式的，所以必须通过 pipe 作为中介，它们也都部署了 Stream 接口，可以使用其方法（on 啦子类的）。

## Child Process

## Koa2

app.use：

将传入的函数存入 this.middleware 数组，如果是 generator 函数还需要首先通过 koa-convert 转换成类似 async/awaith 函数（generator + 自执行）。

app.listen：

`http.createServer(this.callback()).listen(port)`

每次请求都会执行 callback 回调。

app.callback：

组合 fn = koa-compose(middleware)，创建 ctx = createContext(req, res)，返回处理请求的结果 handleRequest(ctx, fn)（洋葱式调用）。

middleware：

通过 app.use 被传入 middleware 的中间件是一个 async 函数，接收参数 ctx 和 next，ctx 封装了 req 和 res，next 用于将程序控制权交给下一个中间件。

koa-compose：

返回一个以 context 和 next 作为参数的函数，在其中递归地执行所有中间件函数，每个函数在遇到 await next 的时候就开始执行下一个函数，所以中间件函数的执行顺序就是由调用栈来决定的，先执行的函数在遇到 await next 之后剩下的部分反而会后执行。等执行到 middleware 数组中最后一个函数的时候...

```js
// usage: 
app.use(async (ctx, next) => {
  console.log(1)
  // 这里的 next 就是 () => dispatch(i + 1)
  // 会递归地去执行下一个中间件函数
  await next
  console.log(2)
})

// test
// 如果不是 async / generator
// 如果不调用 await next
// 如果调用的是 next

function compose (middleware) {
  // 执行所有中间件的方法
  return function (context, next) {
    let index = -1
    return dispatch(0)
    function dispatch (i) {
      index = i
      let fn = middleware[i]
      if (i === middleware.length) fn = next
      if (!fn) return Promise.resolve()
      try {
        // fn 是 async 函数，这里相当于 Promise.resolve 了一个 promise
        return Promise.resolve(fn(context, function next () {
          return dispatch(i + 1)
        }))
      } catch (err) {
        return Promise.reject(err)
      }
    }
  }
}
```

createContext：

把 res、req 及一些 request 的关键属性挂载到 context 上。

```js
createContext(req, res) {
  const context = Object.create(this.context)
  const request = context.request = Object.create(this.request)
  const response = context.response = Object.create(this.response)
  context.app = request.app = response.app = this
  context.req = request.req = response.req = req
  context.res = request.res = response.res = res
  request.ctx = response.ctx = context
  request.response = response
  response.request = request
  context.originalUrl = request.originalUrl = req.url;
  context.cookies = new Cookies(req, res, {
    keys: this.keys,
    secure: request.secure
  })
  request.ip = request.ips[0] || req.socket.remoteAddress || ''
  context.accept = request.accept = accepts(req)
  context.state = {}
  return context
}
```

handleRequest：

```js
handleRequest(ctx, fnMiddleware) {
  const res = ctx.res
  res.statusCode = 404
  const onerror = err => ctx.onerror(err)
  const handleResponse = () => respond(ctx)
  onFinished(res, onerror)
  return fnMiddleware(ctx).then(handleResponse).catch(onerror)
}
```

koa-convert：

将 generator 和用自执行函数包装。

```js
function co(gen) {
  var ctx = this;
  var args = slice.call(arguments, 1);

  // 统一返回一个整体的 promise
  return new Promise(function(resolve, reject) {
    // 如果是函数，调用并取得 generator 对象
    if (typeof gen === 'function') gen = gen.apply(ctx, args);
    // 如果根本不是 generator 对象（没有 next 方法），直接 resolve 掉并返回
    if (!gen || typeof gen.next !== 'function') return resolve(gen);

    // 入口函数
    onFulfilled();

    function onFulfilled(res) {
      var ret;
      try {
        // 拿到 yield 的返回值
        ret = gen.next(res);
      } catch (e) {
        // 如果执行发生错误，直接将 promise reject 掉
        return reject(e);
      }
      // 延续调用链
      next(ret);
    }
    function onRejected(err) {
      var ret;
      try {
        // 如果 promise 被 reject 了就直接抛出错误
        ret = gen.throw(err);
      } catch (e) {
        // 如果执行发生错误，直接将 promise reject 掉
        return reject(e);
      }
      // 延续调用链
      next(ret);
    }
    function next(ret) {
      // generator 函数执行完毕，resolve 掉 promise
      if (ret.done) return resolve(ret.value);
      // 将 value 统一转换为 promise
      var value = toPromise.call(ctx, ret.value);
      // 将 promise 添加 onFulfilled、onRejected，这样当新的promise 状态变成成功或失败，就会调用对应的回调。整个 next 链路就执行下去了
      if (value && isPromise(value)) return value.then(onFulfilled, onRejected);
      // 没法转换为 promise，直接 reject 掉 promise
      return onRejected(new TypeError('You may only yield a function, promise, generator, array, or object, '
        + 'but the following object was passed: "' + String(ret.value) + '"'));
    }
  });
}
```

错误处理：

通过 handleRequest 最后的 `fnMiddleware(ctx).then(handleResponse).catch(onerror)` catch 到执行中间件过程中的错误。

```js
// usage
app.on('error', err => {
  log.error('server error', err)
})

onerror(err) {
  this.app.emit('error', err, this)
}
```

## redux
### connect

是一个返回告高阶函数的高阶函数。

mapStateToProps：

将 store 中的数据作为 props 绑定到组件上。

mapDispatchToProps：

将 action 作为 props 绑定到组件上。

## 高性能组件

react

http://taobaofed.org/blog/2016/08/12/optimized-react-components/