## react 第四部分

### 为什么 isMounted() 是一个反模式，而正确的解决方案是什么?

isMounted() 的主要场景是避免在组件卸载后调用 setState()，因为它会发出警告。

```js
if (this.isMounted()) {
  this.setState({...})
}

```

在调用 setState() 之前检查 isMounted() 会消除警告，但也会破坏警告的目的。使用 isMounted() 有一种代码味道，因为你要检查的唯一原因是你认为在卸载组件后可能持有引用。

最佳解决方案是找到在组件卸载后调用 setState() 的位置，并修复它们。这种情况最常发生在回调中，即组件正在等待某些数据并在数据到达之前卸载。理想情况下，在卸载之前，应在 componentWillUnmount() 中取消任何回调。

### 为什么组件名称应该以大写字母开头?

如果使用 JSX 渲染组件，则该组件的名称必须以大写字母开头，否则 React 将会抛出无法识别标签的错误。这种约定是因为只有 HTML 元素和 SVG 标签可以以小写字母开头。

定义组件类的时候，你可以以小写字母开头，但在导入时应该使用大写字母。

```js
class myComponent extends Component {
  render() {
    return <div />
  }
}

export default myComponent
```

当在另一个文件导入时，应该以大写字母开头：

```js
import MyComponent from './MyComponent'
```

### 在 React v16 中是否支持自定义 DOM 属性?

是的，在过去 React 会忽略未知的 DOM 属性。如果你编写的 JSX 属性 React 无法识别，那么 React 将跳过它。例如

```html
<div mycustomattribute={'something'} />
```
在 React 15 中将在 DOM 中渲染一个空的 div：

```html
<div />
```

在 React 16 中，任何未知的属性都将会在 DOM 显示：

```html
<div mycustomattribute='something' />
```

### constructor 和 getInitialState 有什么区别?

当使用 ES6 类时，你应该在构造函数中初始化状态，而当你使用 React.createClass() 时，就需要使用 getInitialState() 方法。

使用 ES6 类:

```js
class MyComponent extends React.Component {
  constructor(props) {
    super(props)
    this.state = { /* initial state */ }
  }
}
```

使用 React.createClass():

```js
const MyComponent = React.createClass({
  getInitialState() {
    return { /* initial state */ }
  }
})
```

注意： 在 React v16 中 React.createClass() 已被弃用和删除，请改用普通的 JavaScript 类。

### 是否可以在不调用 setState 方法的情况下，强制组件重新渲染?

默认情况下，当组件的状态或属性改变时，组件将重新渲染。如果你的 render() 方法依赖于其他数据，你可以通过调用 forceUpdate() 来告诉 React，当前组件需要重新渲染

```js
component.forceUpdate(callback)
```

建议避免使用 forceUpdate()，并且只在 render() 方法中读取 this.props 和 this.state。

### 在使用 ES6 类的 React 中 super() 和 super(props) 有什么区别?

当你想要在 constructor() 函数中访问 this.props，你需要将 props 传递给 super() 方法。

使用 super(props):

```js
class MyComponent extends React.Component {
  constructor(props) {
    super(props)
    console.log(this.props) // { name: 'John', ... }
  }
}
```

使用 super():

```js
class MyComponent extends React.Component {
  constructor(props) {
    super()
    console.log(this.props) // undefined
  }
}
```

在 constructor() 函数之外，访问 this.props 属性会显示相同的值。
