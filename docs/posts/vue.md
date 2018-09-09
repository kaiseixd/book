# 基础
## 表单

v-model 会根据 `<input>`、`<textarea>` 及 `<select>` 元素的类型自动选取正确的方法来更新元素

会忽略所有表单元素的 value、checked、selected 初始值

可以通过 input 事件让输入中文的时候输入框也能得到更新

### 变量类型

对于文本，绑定的变量类型是字符串

对于复选按钮、单选按钮、选择框，绑定的是选中项的 value 属性：

  - 复选框在变量为数组时是 value，否则会是布尔值
  - 选择框在未设定 value 时值为 option 标签中 innerText

### 修饰符

- .lazy：在 change 事件时才同步
- .number：将输入转为数值类型（现在 type="number" 时好像就已经是数值类型 Chrome）
- .trim

### v-model 的实质

语法糖

实现：

```js
// TODO
```

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

## 数据传递
### 组件通信

1. props 向子组件传递
2. emitter 向父组件传递
3. vuex 状态管理
4. event bus 非父子组件传递（事件监听发布）
5. inheritAttrs + $attrs + $listeners

### Event Bus

### inheritAttrs

interitAttrs（默认值为 true）：默认情况下父作用域（父组件?）的不被认作 props 的特定绑定将会“回退”且作为普通 HTML 特性应用在子组件的根元素上，而 $attrs 可以让这些特性显性的绑定到非根元素上

> 为true时，所有没在子元素props中的属性都会应用到子元素的根元素上

$attrs：包含了父作用域中不作为 prop 被识别（不是 prop 都可以）的特性绑定 (class 和 style 除外)，通过 v-bind="$attrs" 传入内部组件（多级组件嵌套也可以）

> 除prop外都传递

$listeners：包含了父作用域中的 (不含 .native 修饰器的) v-on 事件监听器。它可以通过 v-on="$listeners" 传入内部组件——在创建更高层次的组件时非常有用。

## 组件

文档：组件是可复用的 Vue 实例，且带有名字（大写会按驼峰命名法处理）

```js
// 注册组件，传入一个扩展过的构造器
Vue.component('my-component', Vue.extend({ /* ... */ }))

// 注册组件，传入一个选项对象 (自动调用 Vue.extend)
Vue.component('my-component', { /* ... */ })

// 获取注册的组件 (始终返回构造器)
var MyComponent = Vue.component('my-component')

new Vue({
  el: '#app',
  components: { 
    'my-component-also-ok': MyComponent,
    // 试了一下直接传一个对象也行...
    'onlyTemplate': { template: '<div>123</div>' }
  }
})
```

### :is

用于动态组件，或者解决模板解析的限制问题（ul内必须为li等）

```js
<component v-bind:is="tag"></component>

<table>
  <tr is="my-row"></tr>
</table>
```

## 生命周期

Vue 实例在被创建时经过的一系列初始化过程，并且在这个过程的不同阶段会执行对应的钩子函数

### 钩子

- `beforeCreate`：在实例初始化之后，数据观测（props、data、computed）和 event/watcher 事件配置（mixin?）之前被调用
- `created`：实例创建完成后立即调用，此时已完成数据观测，属性和方法的运算，watch/event 事件回调（data、methods、props 等已经挂到实例上），但是还没开始挂载 DOM，$el 属性目前不可见
- `beforeMount`：模板编译完成，但未挂载，无法获取 DOM，此时首次调用 render 函数
- `mounted`：挂载完成，能获取 DOM
- `beforeUpdate`
- `updated`
- `activated`
- `deactivated`
- `beforeDestroy`
- `destroyed`：用于组件变更时的状态存储或内容释放
- `errorCaptured`

## 混入

文档：混入 (mixins) 是一种分发 Vue 组件中可复用功能的非常灵活的方式。混入对象可以包含任意组件选项。当组件使用混入对象时，所有混入对象的选项将被混入该组件本身的选项。

```js
new Vue({
  el: '#app',
  mixins: [{ data() { return { a: 1 } } }]
})
```

`Vue.config.optionMergeStrategies`：自定义合并策略

### 选项合并

能合并就合并（对象的属性也会被合并），不能合并就优先组件数据

混入对象的钩子将在组件自身的钩子之前调用

### 全局混入

将会影响到所有之后创建的 Vue 实例，使用恰当时，可以为自定义对象注入处理逻辑

```js
Vue.mixin({ ... })
```

## 自定义指令

对普通 DOM 元素进行额外的底层操作

```js
// 全局自定义指令 v-focus
Vue.directive('focus', { ... })
// 第二个参数还是可以是函数简写（只在 bind 和 update 时触发?）
Vue.directive('focus', function (el, binding) { expr })

// 局部指令（作为组件选项）
directives: {
  focus: { ... }
}
```

### 钩子函数

- bind：只调用一次，指令第一次绑定到元素时调用。在这里可以进行一次性的初始化设置
- inserted：被绑定元素插入父节点时调用 (仅保证父节点存在，但不一定已被插入文档中)
- update：所在组件的 VNode 更新时调用，但是可能发生在其子 VNode 更新之前。指令的值可能发生了改变，也可能没有
- componentUpdated：指令所在组件的 VNode 及其子 VNode 全部更新后调用
- unbind：只调用一次，指令与元素解绑时调用

### 参数

- el：元素 DOM
- binding：跟指令有关的对象
- vnode
- oldVnode

> 除了 el 之外，其它参数都应该是只读的，切勿进行修改

## 插件

Vue.js 的插件应当有一个公开方法 `install(Vue, options)`，将插件要添加的部分写入其中

之后通过 `Vue.use()` 使用插件（调用 `plugin.install(Vue)`）

### 范围

1. 添加全局方法或者属性
2. 添加全局资源：指令/过滤器/过渡等
3. 通过全局 mixin 方法添加一些组件选项
4. 添加 Vue 实例方法，通过把它们添加到 Vue.prototype 上实现
5. 一个库，提供自己的 API，同时提供上面提到的一个或多个功能

## 渲染函数

Vue.compile(template)

有待补充

## 全局 API
### Vue.extend( options )

可以构建一个子类，参数是一个包含组件选项（以及特例 data）的对象

```js
var Component = Vue.extend({
  mixins: [myMixin],
  data () {
    return {
      alias: 'Heisenberg'
    }
  }
})

// 在全局 component 中注册
Vue.component('apple', Component)

// 在实例的 components 中注册
new Vue({
  components: {
    apple: Component
  }
})

// 通过 $mount 注册
new Component().$mount('#app')
// 等同
new Component({ el: '#app' })
```

Vue.extend 返回的是一个“扩展实例构造器”，也就是一个预设了部分选项的 Vue 实例构造器（[点我](https://segmentfault.com/q/1010000007312426)）

```js
var myVue = Vue.extend({
  // 预设选项
}) // 返回一个“扩展实例构造器”

// 然后就可以这样来使用
var vm = new myVue({
  // 其他选项
})
```

### Vue.nextTick( [callback, context] )

文档：在下次 DOM 更新循环结束之后执行延迟回调。在修改数据之后立即使用这个方法，获取更新后的 DOM。

简单来说，Vue 在修改数据之后，视图不会立刻更新，而是会在 microtask 执行完之后才更新（就是 ui render），而 nextTick 中的 callback 会在这之后执行。

在 created 和 mounted 阶段，如果需要操作渲染后的试图，也要使用 nextTick 方法。

Vue 在内部会尝试对异步队列使用原生的 Promise.then 和 MessageChannel（还是 MutationObserver?），如果执行环境不支持，会采用 setTimeout(fn, 0)代替。

### Vue.set( target, key, value )

向响应式对象中添加一个属性，并确保这个新属性同样是响应式的，且触发视图更新

> Vue 无法探测普通的新增属性（已经存在的属性被重新赋值是能探测到的）

### Vue.delete( target, key )

响应式删除，并触发视图更新（delete 是不更新的），key 也可以是 array 的 index

### Vue.version

提供字符串形式的 Vue 安装版本号

## 选项/数据
### data

实例的数据对象，在实例创建时将会递归地把 data 的属性转换为 getter/setter（除了这种方式以及 Vue.set 之外，新增的对象或者属性都将不是响应式的）

实例创建之后，可以通过 vm.$data 访问原始数据对象。Vue 实例也代理了 data 对象上所有的属性（以 _ 或 $ 开头的属性不会被代理，只能用 vm.$data 访问），因此访问 vm.a 等价于访问 vm.$data.a

### props

类型：Array<string> | Object

验证可选属性：type, default, required, validator（默认 type）

### propsData

类型：{ [key: string]: any }

在 new 创建的实例中传递 props（测试用?）

### computed

类型：{ [key: string]: Function | { get: Function, set: Function } }

计算属性会被混入到 Vue 实例中，所有 getter 和 setter 的 this 上下文自动地绑定为 Vue 实例

计算属性的结果会被缓存，除非依赖的响应式属性变化才会重新计算

### methods

类型：{ [key: string]: Function }

可以通过 Vue 实例访问到这些方法，方法中的 this 自动绑定为 Vue 实例，所以如果方法是箭头函数的话 this 将会是父级作用域（实例所在作用域）上下文

```js
vm[key] = methods[key] == null ? noop : bind(methods[key], vm)
```

### watch

类型：{ [key: string]: string | Function | Object | Array }

键是需要观察的表达式，值是对应回调函数，也可以是方法名，或者包含选项的对象（选项：deep, immediate）

Vue 实例将会在实例化时调用 $watch()，遍历 watch 对象的每一个属性

## 选项/DOM
### el

类型：string | HTMLElement

在由 new 创建的实例中指定挂在的 DOM，之后可由 `vm.$el` 访问

如果在实例化时存在这个选项，实例将立即进入编译过程，否则，需要显式调用 vm.$mount() 手动开启编译

> render 函数或 template 属性存在的话会替换挂载的元素，否则提取挂载 DOM 元素的 HTML 用作模板，此时必须使用 Runtime + Compiler 构建的 Vue 库

## 选项/组合
### parent

类型：Vue 实例

指定已创建的实例之父实例，在两者之间建立父子关系。子实例可以用 this.$parent 访问父实例，子实例被推入父实例的 $children 数组中。

> 不太建议使用

### extends

类型：Object | Function

允许声明扩展另一个组件(可以是一个简单的选项对象或构造函数)，而无需使用 Vue.extend。

### provide/inject

provide：Object | () => Object
inject：Array<string> | { [key: string]: string | Symbol | Object }

这对选项需要一起使用，以允许一个祖先组件向其所有子孙后代注入一个依赖，不论组件层次有多深，并在起上下游关系成立的时间里始终生效

provide 对象包含提供给其子孙的属性

inject 对象包含子组件注入的属性

> provide 和 inject 主要为高阶插件/组件库提供用例，并不推荐直接用于应用程序代码中

> provide 和 inject 绑定并不是可响应的。这是刻意为之的。然而，如果你传入了一个可监听的对象，那么其对象的属性还是可响应的。

## 选项/其他
### model

允许一个自定义组件在使用 v-model 时定制 prop 和 event，默认情况下把 value 作为 prop，input 作为 event

## 实例属性

list：vm.$data, vm.$props, vm.$el, vm.$parent, vm.$root（根 Vue 实例）, vm.$children, vm.$isServer, vm.$refs

### vm.$options

实例初始化时的选项，在选项中包含自定义属性时有用

### vm.$slots

用来访问被插槽分发的内容。每个具名插槽 有其相应的属性 (例如：slot="foo" 中的内容将会在 vm.$slots.foo 中被找到)。default 属性包括了所有没有被包含在具名插槽中的节点。

> 在使用渲染函数书写一个组件时有用

### vm.$scopedSlots

用来访问作用域插槽。对于包括 默认 slot 在内的每一个插槽，该对象都包含一个返回相应 VNode 的函数。

> 在使用渲染函数开发一个组件时特别有用

### vm.$attrs

包含了父作用域中不作为 prop 被识别 (且获取) 的特性绑定 (class 和 style 除外)。当一个组件没有声明任何 prop 时，这里会包含所有父作用域的绑定 (class 和 style 除外)，并且可以通过 v-bind="$attrs" 传入内部组件。

> 在创建更高层次的组件时非常有用

### vm.$listeners

包含了父作用域中的 (不含 .native 修饰器的) v-on 事件监听器。它可以通过 v-on="$listeners" 传入内部组件

> 在创建更高层次的组件时非常有用

## 实例方法/生命周期
### vm.$mount( [elementOrSelector] )

手动挂载一个未挂载（没有收到 el 选项）的实例

### vm.$forceUpdate

迫使 Vue 实例重新渲染，仅影响实例本身和插入插槽内容的子组件

## 指令
### v-show

切换元素的 display 属性，会触发过渡效果

### v-if

在切换时元素及它的数据绑定 / 组件被销毁并重建

触发过渡效果，优先级比 v-for 低

### v-bind

修饰符：

- .prop
- .camel
- .sync

### v-cloak

这个指令保持在元素上直到关联实例结束编译

### v-once

只渲染元素和组件一次。随后的重新渲染，元素/组件及其所有的子节点将被视为静态内容并跳过。这可以用于优化更新性能

## 特殊特性
### key

主要用在 Vue 的虚拟 DOM 算法，在新旧 nodes 对比时辨识 VNodes。如果不使用 key，Vue 会使用一种最大限度减少动态元素并且尽可能的尝试修复/再利用相同类型元素的算法。使用 key，它会基于 key 的变化重新排列元素顺序，并且会移除 key 不存在的元素。

它也可以用于强制替换元素/组件而不是重复使用它（触发钩子或者过渡）

### ref

ref 被用来给元素或子组件注册引用信息。引用信息将会注册在父组件的 $refs 对象上。如果在普通的 DOM 元素上使用，引用指向的就是 DOM 元素；如果用在子组件上，引用就指向组件实例

### slot-scope

用于将元素或组件表示为作用域插槽

## 备忘

```js
const childComment = () => import('./childCom.vue')
```

- 使用 less 需要 style-loader / less-loader / css-loader / less

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