# 基础
## 元素渲染

元素（普通对象）是构成 React 应用的最小单位（构成组件的一部分）

### 渲染到 DOM 中

ReactDOM.render(element, root)

### 更新元素渲染

React 元素都是immutable 不可变的。当元素被创建之后，你是无法改变其内容或属性的。所以只能创建一个新元素传入 render 函数。

## 组件 & Props

函数定义组件：接收一个单一的 props 对象并返回一个 React 元素

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
