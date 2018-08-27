# Vue
## 数据绑定

Vue 的数据绑定基于数据劫持，有两种实现 Object.defineProperty 和 Proxy

### 优势

1. 无需显示调用：数据劫持 + 发布订阅可以直接通知变化并驱动视图

2. 可精确得知变化数据：由于劫持了 setter ，属性改变的时候可以精确获知 newVal ，就不需要额外的 diff 操作

### 思路

1. 利用Proxy或Object.defineProperty生成的Observer针对对象/对象的属性进行"劫持",在属性发生变化后通知订阅者
2. 解析器Compile解析模板中的Directive(指令)，收集指令所依赖的方法和数据,等待数据变化然后进行渲染
3. Watcher属于Observer和Compile桥梁,它将接收到的Observer产生的数据变化,并根据Compile提供的指令进行视图渲染,使得数据变化促使视图变化

> 由于一次 defineProperty 只能定义一个属性，并且对于不同的 setter 和 getter 每次都要深入方法内部去修改，耦合严重，因此需要加入发布订阅模式

> defineProperty 存在的问题：只能监听对象的属性、无法监听的数组的变化，这些在 Proxy 上都被解决

### 实现

```js
let uid = 0
class Dep {
  constructor () {}
}
```

## 数据传递
### 组件通信

1. 父子组件用 Props 通信
2. 非父子组件用 Event Bus 通信
3. Vuex 全局状态管理

### Event Bus


# 源码
## 数据驱动
### new Vue(option)

主要介绍 new Vue() 之后 data 是如何绑定的

首先从入口开始，在 `src/core/instance/index.js` 中调用了 `this._init` 进行初始化

```js
function Vue (options) {
  // ...
  this._init(options)
}
```

`this._init` 在 `src/core/instance/init.js` 中定义

Vue 的初始化主要就干了几件事情，合并配置，初始化生命周期，初始化事件中心，初始化渲染，初始化 data、props、computed、watcher 等等，并在最后调用 `$mount` 进行挂载

```js
Vue.prototype._init = function (options?: Object) {
  // ...
  if (options && options._isComponent) {
    // optimize internal component instantiation
    // since dynamic options merging is pretty slow, and none of the
    // internal component options needs special treatment.
    initInternalComponent(vm, options)
  } else {
    vm.$options = mergeOptions(
      resolveConstructorOptions(vm.constructor),
      options || {},
      vm
    )
  }
  // ...
  // expose real self
  vm._self = vm
  initLifecycle(vm)
  initEvents(vm)
  initRender(vm)
  callHook(vm, 'beforeCreate')
  initInjections(vm) // resolve injections before data/props
  initState(vm)
  initProvide(vm) // resolve provide after data/props
  callHook(vm, 'created')
  // ...
  vm.$mount(vm.$options.el)
}
```

data 的初始化在 `src/core/instance/state.js` 的 `initState(vm)` 中，这个函数根据 `vm.$options` 初始化 props、methods、data、computed、watch

```js
export function initState (vm: Component) {
  vm._watchers = []
  const opts = vm.$options
  if (opts.props) initProps(vm, opts.props)
  if (opts.methods) initMethods(vm, opts.methods)
  if (opts.data) {
    initData(vm)
  } else {
    observe(vm._data = {}, true /* asRootData */)
  }
  if (opts.computed) initComputed(vm, opts.computed)
  if (opts.watch && opts.watch !== nativeWatch) {
    initWatch(vm, opts.watch)
  }
}
```

在 `initData` 中，data 如果是函数的话会调用 `getData(data, vm)`；之后 data 会被赋值给 vm._data

步骤：

1. 判断是否有同名 methods（有的话好像只是报错） 和 props（有的话采用 props）
2. 对 data 进行 proxy 和 observe

```js
function initData (vm: Component) {
  let data = vm.$options.data
  data = vm._data = typeof data === 'function'
    ? getData(data, vm)
    : data || {}
  // ...
  proxy(vm, `_data`, key)
  // ...
  observe(data, true /* asRootData */)
}
```

`getData` 调用了 `data.call(vm, vm)`

```js
export function getData (data: Function, vm: Component): any {
  // ...
  return data.call(vm, vm)
}
```

`proxy` 给对应的 `vm[sourceKey][key]` 设置 setter 和 getter （这里的 sourceKey 就是 _data）

```js
const sharedPropertyDefinition = {
  enumerable: true,
  configurable: true,
  get: noop,
  set: noop
}

export function proxy (target: Object, sourceKey: string, key: string) {
  sharedPropertyDefinition.get = function proxyGetter () {
    return this[sourceKey][key]
  }
  sharedPropertyDefinition.set = function proxySetter (val) {
    this[sourceKey][key] = val
  }
  Object.defineProperty(target, key, sharedPropertyDefinition)
}
```

`observe` 将对 data 进行响应式处理，这里先不提

### 实例挂载（$mount）

`$mount` 这个方法的实现是和平台、构建方式有关，这里只提 `src/platform/web/entry-runtime-with-compiler.js` 中的挂载，其中参数 `hydrating` 和服务器渲染有关

步骤：

1. 缓存原型上的 `$mount` ，再重新定义该方法
2. 调用 `query` 获取 el 对应的 dom 节点
3. 如果没有定义 `render` 方法，则会把 el（在没有 template 时） 或者 template 字符串转换成 `render` 方法（调用 `compileToFunctions`）
4. 用缓存的原型上的 `$mount` 挂载

```js
const mount = Vue.prototype.$mount
Vue.prototype.$mount = function (el, hydrating) {
  el = el && query(el)
  // ...
  template = (string | id | el)ToTemplate()
  // ...
  const { render, staticRenderFns } = compileToFunctions(template, { ... }, this)
  // ...
  return mount.call(this, el, hydrating)
}
```

原先原型上的 `$mount` 方法在 `src/platform/web/runtime/index.js` 中定义，之所以这么设计完全是为了复用，因为它是可以被 runtime only 版本的 Vue 直接使用的

步骤：

1. 返回 `src/core/instance/lifecycle.js` 中的 `mountComponent()` 的执行结果

```js
// public mount method
Vue.prototype.$mount = function (el, hydrating) {
  el = el && inBrowser ? query(el) : undefined
  return mountComponent(this, el, hydrating)
}
```

`mountComponent` 核心就是先调用 `vm._render` 方法先生成虚拟 Node，再实例化一个渲染 Watcher，在它的回调函数中会调用 `updateComponent` 方法，最终调用 `vm._update` 更新 DOM

Watcher 在这里起到两个作用，一个是初始化的时候会执行回调函数，另一个是当 vm 实例中的监测的数据发生变化的时候执行回调函数

函数最后判断为根节点的时候设置 `vm._isMounted` 为 true， 表示这个实例已经挂载了，同时执行 `mounted` 钩子函数。 这里注意 `vm.$vnode` 表示 Vue 实例的父虚拟 Node，所以它为 Null 则表示当前是根 Vue 的实例

### render

Vue 的 `_render` 方法是实例的一个私有方法，它用来把实例渲染成一个虚拟 Node ，它的定义在 `src/core/instance/render.js` 文件中

主要调用了之前 `mounted` 实现的 `render` 函数，最终执行 `createElement` 并返回 vnode

### Virtual Dom

由于浏览器的 DOM 太庞大，所以使用原生 JS 对象去描述一个 DOM 节点，即 Virtual DOM

## 学不来

```js
// 1
let keys = Object.keys(data)
let i = keys.length
while (i--) {
  const key = keys[i]
}
```