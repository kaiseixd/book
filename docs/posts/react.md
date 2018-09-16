# 基础
## 元素渲染

元素（普通对象）是构成 React 应用的最小单位（构成组件的一部分）

### 渲染到 DOM 中

ReactDOM.render(element, root)

### 更新元素渲染

React 元素都是immutable 不可变的。当元素被创建之后，你是无法改变其内容或属性的。所以只能创建一个新元素传入 render 函数。

## 组件 & Props
### 函数定义组件

接收一个单一的 props 对象并返回一个 React 元素

也可以用 class 来定义，需要继承 React.Component

```js
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

### 自定义组件

标签首字母大写的自定义元素，会把 JSX 属性作为单个对象（props）传递给组件（和标签名字一样的函数定义组件）

```js
// 对应函数定义组件 Welcome
const element = <Welcome name="Sara" />
```

### 组合组件

```js
// <App /> 对应函数组件 App，<Welcome /> 对应函数组件 Welcome
// 这个括号...
function App() {
  return (
    <div>
      <Welcome name="Sara" />
      <Welcome name="Cahal" />
      <Welcome name="Edite" />
    </div>
  );
}

React.render(<app />, el)
```

### 组件渲染

1. render 传给 ReactDOM.render 的第一个参数：element（需要是一个标签或者元素）
2. 对于自定义组件，找到对应的函数定义组件，传入 props 参数，将其返回的元素作为自定义组件的元素
3. 更新 DOM

### tip

- 组件的返回值只能有一个根元素，需要的话要用 div 包裹
- props 被期望是只读的，不要修改

> 函数或者 class 组件，render 的时候传入 <El />，如果已经是元素了，传入 el 即可

## State & 生命周期

使用类来定义组件才能使用它的特性，比如局部状态、生命周期钩子（继承自 React.Component）

### 添加局部状态和生命周期

```js
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    );
  }

  componentWillUnmount() {
    clearInterval(this.timerID);
  }

  tick () {
    this.setState({
      date: new Date()
    });
  }

  render() {
    return (
      <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
    );
  }
}
```

### setState

直接修改 this.state.date 并不会重新渲染组件，需要使用 setState()

构造函数是唯一能够初始化 this.state 的地方

React 可以将多个setState() 调用合并成一个调用来提高性能。因为 this.props 和 this.state 可能是异步更新的，你不应该依靠它们的值来计算下一个状态。需要给 setState 提供一个函数来修复这个问题，第一个参数是应用前状态，第二个参数是应用后状态。

### 数据自顶向下流动

组件可以选择将其状态作为属性传递给其子组件，子组件还是用 props 接收

## 事件处理

`<button onClick={activateLasers}>`

不过不同的是 React 不能使用 return false 来阻止默认行为，必须明确使用 e.preventDefault

需要注意绑定好 this，`this.handleClick = this.handleClick.bind(this);`

> 只要函数里有用到 this 的话就需要在 constructor 中 bind 一下 this

### 不绑定 this

不想绑定 this 的话可以使用属性初始化器语法，或者在绑定事件的时候直接使用箭头函数写好

```js
// 箭头函数
touch = () => this.bark()

// 直接绑定
<div onClick={()=>this.bark()}>
<div onClick={this.bark.bind(this)}>
```

### 传递参数

使用箭头函数时事件参数要显示传递

使用 bind 方式使会隐式传递，但是定义事件处理函数的时候 e 要放在参数的最后面

## 条件渲染

使用 JavaScript 操作符 if 或条件运算符来创建表示当前状态的元素（将元素作为不同条件的 return 值），然后让 React 根据它们来更新 UI。

如果不想让组件渲染的话，可以让函数定义组件 return null

### || and &&

可以通过用花括号包裹代码在 JSX 中嵌入表达式以及元素，还可以根据逻辑运算符有条件地渲染

```js
{ me.length > 0 && <h2> shows when length > 0 </h2> }
```

## 列表 & Keys
### 渲染多个组件

```js
const numbers = [1, 2, 3, 4, 5];
const listItems = numbers.map((number) =>
  <li>{number}</li>
);

// 以及
const listItems = (
  <ul>
    {numbers.map((number) =>
      <ListItem key={number.toString()}
                value={number} />

    )}
  </ul>
)
```
### Keys

Keys可以在DOM中的某些元素被增加或删除的时候帮助React识别哪些元素发生了变化。

元素的key只有在它和它的兄弟节点对比时才有意义。

## 表单
### 受控组件

```js
<form onSubmit={this.handleSubmit}></form>

<input type="text" value={this.state.value} onChange={this.handleChange} />

// 限制输入都是大写字母
handleChange(event) {
  this.setState({value: event.target.value.toUpperCase()});
}
```

### 多个输入

根据 event.target.name 的值来选择改变哪个输入框

```js
this.setState({
  [name]: value
});
```

## 状态提升

将共享状态提升至最近的父组件当中进行管理

流程：子组件 onchange 的时候调用 this.handleChange，这是从父组件 prop 来的：this.props.onTemperatureChange，之后父组件指定该函数的值为 onTemperatureChange={this.handleFahrenheitChange}，并在 handleFahrenheitChange 函数中给目标 scale setState

## 组合 vs 继承

React 具有强大的组合模型，我们建议使用组合而不是继承来复用组件之间的代码

props.children

# Redux
## 基础

- Store：保存数据的地方，对外不可见，只能 getState 获取某时刻数据，createStore()
- State：某个时刻的数据，一个state对应一个view
  - store.getState()
  - store.dispatch()：发出action
- Action：view发出通知，表示state要变化（state可以直接改变view，view通过action改变state）
- Action Creator：生成action的函数（给特定type的action？）
- Reducer：Reducer 是一个函数，它接受 Action 和当前 State 作为参数，返回一个新的 State（这个 state 需要浅复制原来的 state，以便重新 render 时能找出变化的地方）
  - reducer 不允许有副作用

# example

```js
// 1 函数式组件
const getDefaultStyledPost = defaultStyle => {
  return props => <p style={{...defaultStyle, ...props.style}}>123</p>
}
const Post = getDefaultStyledPost({ color: 'red', 'fontSize': '20px' })

ReactDOM.render(<Post style={{color: 'blue'}}/>, document.getElementById('root'))
```

# point & api

- props.children：包含组件的开始和结束标记之间的内容
- componentWillMount 是在组件 mount 之前被调用，所以在 render 之前


```js
// 对象解构赋值
var props = {
  title: 't',
  style: {
    color: 'red',
    fontSize: '18px'
  }
}
var { style } = props

var props2 = {
  content: 'a',
  ...props
}

<Component {...props} />
```