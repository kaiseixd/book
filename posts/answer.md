# 如何面试
* https://juejin.im/post/5bbc54a2e51d450e5a7445b4

# css
## 垂直居中
* 行内：text-align, vertical-align
* 块级元素：`margin: 0 auto;`
  * 因为正常流块级元素水平宽度（等同于父元素 width）取决于 ml + pl + bl + width + br + pr + mr（只有 margin 和 width 可以设置为 auto），宽度有确定值时 auto 的 margin 就会填充剩余空间。
  * 但是垂直方向是不行的，即使块级元素和父元素的高度确定。
* 定位：`position: absolute;`
* flex
* table-cell

# js
## this
* this 引用的是函数执行的环境对象。
* 箭头函数的 this，因为没有自身的 this，所以 this 只能根据作用域链往上层查找，直到找到一个绑定了 this 的函数作用域（即最靠近箭头函数的普通函数作用域，或者全局环境），并指向调用该普通函数的对象。

## 闭包
https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Closures

* 闭包指的是函数内部的变量能够访问到外部作用域，也就是创建这个函数时的作用域，中的所有变量。即使函数是在它的作用域之外的地方执行。

## 原型
函数的 prototype 属性，是一个对象，拥有 constructor 属性指向构造函数。实例的 `__proto__` 会指向原型，也可以使用 `Object.getPrototypeOf` 获取原型。

### 原型链
读取实例的属性时，也会在原型中查找，如果还找不到会在原型的原型中查找，直到顶层（`Object.prototype.__proto__ === null`）。

### 是继承吗
委托可能更为恰当。

### reference
[JavaScript深入之从原型到原型链](https://github.com/mqyqingfeng/Blog/issues/2)

## 作用域
作用域规定了如何查找变量，也就是确定当前执行代码对变量的访问权限。
JavaScript 是词法作用域，函数的作用域在函数定义的时候确定。

## 执行上下文
执行函数的时候会创建执行上下文，并压入 ECStack（execution context），执行完再弹出。栈底永远是 globalContext。

执行上下文包含：
1. 变量对象
2. 作用域链
3. this

### 变量对象
存储了在上下文中定义的变量和函数声明。
进入该执行上下文时，变量对象变为活动对象，此时其中的变量可以访问。

### 作用域链


### reference
[JavaScript深入之变量对象](https://github.com/mqyqingfeng/Blog/issues/5)

## 继承

## event loop
由浏览器（JavaScript Runtime）维护。Runtime 负责函数的压栈和出栈，JS Engine 负责执行栈顶函数。

### task queue
task：
1. DOM manipulation（DOM 操作）；
2. User interaction（用户交互）；
3. Networking（网络请求）；
4. History traversal（History API 操作）；
5. GUI 渲染。

source：
鼠标、键盘事件，AJAX，数据库操作，setTimeout，setInterval

规定：
1. 来自相同 Task Source 的 Task，必须放在同一个 Task Queue 中；
2. 来自不同 Task Source 的 Task，可以放在不同的 Task Queue 中；
3. 同一个 Task Queue 内的 Task 是按顺序执行的；
4. 但对于不同的 Task Queue（Task Source），浏览器会进行调度，允许优先执行来自特定 Task Source 的 Task。

也就是不同 source 的 task 可能不会按顺序执行（比如鼠标、键盘事件会有更高的优先级）。

### microtask queue
task：
1. promise
2. MutationObserver

microtask 只有一个 queue。

### rendering
1. requestAnimationFrame callback
2. intersection observer
3. render ui

### runtime运行机制
1. 执行同步任务，创建执行上下文，按顺序进入执行栈；
2. 对于异步任务，将对应 task 添加到 event loop 的 queue 中，由其他线程执行具体的异步操作（浏览器内核是多线程的）；
3. 执行栈为空后读取 event loop 中的 queue，取出并执行任务；
4. 重复以上步骤。

### event loop模型
1. 从 task queue 中取出最老的一个 task 并执行（并且一次 event loop 仅执行这一个 task）
2. 执行 microtask queue 中的所有 microtask（microtask 检查点）
3. 进入更新渲染阶段（详细在 reference 里面有）

### microtask queue执行时机
除了模型中的情况（某个 task 执行完）以为还有些情况也会触发执行（等到执行栈为空）：
1. 进入脚本执行的清理阶段
2. 创建和插入节点时
3. 解析 XML 文档时

### reference
[深入理解 JavaScript Event Loop](https://zhuanlan.zhihu.com/p/34229323)

# React
## hooks
### 如何实现
实际上是将依赖放到了一个数组里，所以必须要在最顶层调用 hooks，根据顺序创建依赖。

#### reference
[深入理解：React hooks是如何工作的？](https://zhuanlan.zhihu.com/p/81528320)[再次阅读]

### useEffect
在其中执行副作用操作。需要注意 useEffect 在每轮的渲染之后才会执行，有同步需求可以使用 useLayoutEffect，执行时机在 dom 变更之后、浏览器重新绘制之前。

#### useEffect的闭包问题
useEffect 中的回调函数拿到的 state 是该次渲染的 state，而不是最新渲染的 state。

解决办法：
* 使用 useReducer

### hooks 生成新函数对性能的影响
* 函数在静态编译时期就已经创建了，后续执行需要重新生成的只有环境（闭包）
  * 那么 class 呢
  * [?](https://www.zhihu.com/question/345689944)
* 实际上节省了创建 class 的开销（像是创建类实例和在构造函数中绑定事件处理器的成本）
* 符合语言习惯的代码在使用 Hook 时不需要很深的组件树嵌套
* 使用 useCallback


<!-- 生态 -->
# React-Redux
## 为什么不使用 hooks api
* context api 的性能问题
  * React 会遍历Provider的所有子节点，并对监听了这一个 context 的节点进行标记，让后续渲染中知道这个节点是需要更新的，即便他的 props 和 state 根本没有变化
  * [优化探讨](https://github.com/facebook/react/issues/14110)
* connect HOC 对 state 数据变化做了监听

# 浏览器
## 缓存
https://www.yuque.com/kaisei/note/hbnmnw

# 网络
## http
### 状态码
* 101：协议升级
* 304：协商缓存（etag，last-modified）

### 301和302的区别
* 301：永久重定向，客户端可以对结果进行缓存，下次不必发这个请求（google.com 跳 www.google.com）
* 302：临时重定向，需要请求新url但是不会被缓存（需要指定cache-control或者expires才有缓存，并且语义不如下面的明确）
* 303：临时重定向，并使用GET方法请求新url
  * 常用于将 post 请求重定向到 get 请求，如在 post 数据后导向上传成功页面
* 307：临时重定向（hsts跳转），并使用原方法请求新url（用于 http 跳 https）
  * 下次访问站点直接使用 https，而不是 http 再转 https（但是还是无法阻止第一次访问该网站时的 https 降级攻击）
* 308：永久重定向，不允许将 post 请求重定向到 get 请求上

#### 历史原因
最开始浏览器实现 302 的时候分别实现成了 303 和 307 的效果，所以将它们改到 303 和 307，而 302 本身则被修订为不再需要维持原请求的方法。

#### reference
[HTTP 301 和 302 跳转的本质区别?](https://www.v2ex.com/t/382599)
[HTTP 中的 301、302、303、307、308 响应状态码](https://zhuanlan.zhihu.com/p/60669395)

### get & post
* get 参数有大小限制（因为是 url 传递），post 有多种编码
* get 请求可以缓存
* 按照 RESTful 规范来说 get 一般是读取而 post 一般是创建

### 列出请求方法
* get：请求资源
* post：提交实体，一般会导致服务器状态变化或者有副作用
* put：替换目标资源为最新
* delete：删除目标资源
* connect：？
* options：发送预检请求
* trace：主要用于请求的测试和诊断
* patch：部分资源更新，或者在资源不存在时创建一个

## 安全
### https协议降级攻击
#### reference
[HTTPS 协议降级攻击原理](https://zhuanlan.zhihu.com/p/22917510)

# exercise
## 获取页面中的所有图片src
* 获取所有node：document.querySelectorAll('*')
* 获取node所有样式：window.getComputedStyle(node, null).getPropertyValue('background-image')
* 匹配`url(...)`中的内容

## table长列表怎么实现
https://zhuanlan.zhihu.com/p/26022258
https://zhuanlan.zhihu.com/p/34585166
https://github.com/dwqs/blog/issues/72
https://github.com/chenqf/frontEndBlog/issues/16

## 实现一个table
https://zhuanlan.zhihu.com/p/29027477

## 前端监控
https://zhuanlan.zhihu.com/p/38637451
https://juejin.im/post/5a3e121451882533f01ec66d
https://www.zhihu.com/question/29953354
https://juejin.im/post/5a52f138f265da3e5b32a41b
https://segmentfault.com/a/1190000015864670

## 网络
https://imququ.com/post/sth-about-switch-to-https.html
https://ye11ow.gitbooks.io/http2-explained/content/part1.html
https://www.cnblogs.com/vajoy/p/5341664.html
http://www.alloyteam.com/2016/07/httphttp2-0spdyhttps-reading-this-is-enough/
