# 组件与 Props

组件是 React 应用程序的基本构建块。它们是独立且可复用的代码片段，组合在一起构成用户界面。本节将介绍如何创建函数组件和类组件，以及如何使用 props 在它们之间传递数据。

## 理解组件

在 React 中，组件本质上是一个 JavaScript 函数或类，它返回一个 React 元素，用于描述屏幕上应显示的内容。组件允许你将 UI 拆分为独立、可复用的部分，从而可以独立思考每个部分。

下图展示了父组件和子组件之间的基本关系：

```d2
direction: down

ParentComponent: {
  shape: rectangle
  "State or Data: { user: 'Jane' }"
}

ChildComponent: {
  shape: rectangle
  "Receives props and renders UI"
}

ParentComponent -> ChildComponent: "Passes data via props (e.g., name={user})"
```

### 函数组件

函数组件是在 React 中定义组件的现代且推荐的方式。它们是简单的 JavaScript 函数，接受一个包含数据的 `props` 对象作为参数，并返回一个 React 元素。

```javascript
// 一个简单的函数组件
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

// 用法
const element = <Welcome name="Sara" />;
```

### 类组件

类组件是使用 ES6 类定义组件的另一种方式。它们继承自 `React.Component`，并且必须包含一个 `render()` 方法。

从 React 源码中可以看出，基础 `Component` 类使用 `props`、`context` 和 `updater` 进行初始化。

```javascript
// 源自 src/ReactBaseClasses.js
function Component(props, context, updater) {
  this.props = props;
  this.context = context;
  this.refs = emptyObject;
  this.updater = updater || ReactNoopUpdateQueue;
}

Component.prototype.isReactComponent = {};
```

以下是一个类组件的基本示例：

```javascript
import React, { Component } from 'react';

class Welcome extends Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

类组件具有局部 state 和生命周期方法等附加功能，不过现在 Hooks 也为函数组件提供了这些功能。

#### `setState()`

此方法用于更新组件的局部 state。它告诉 React 该组件及其子组件需要使用更新后的 state 进行重新渲染。

- **异步性**：对 `setState` 的调用是异步的，并且可能会为了性能而进行批处理。在调用 `setState` 后立即访问 `this.state` 可能不会得到新值。
- **函数式更新**：你可以向 `setState` 传递一个函数，以确保更新是基于最新的 state 和 props。

```javascript
// 源自 src/ReactBaseClasses.js
Component.prototype.setState = function (partialState, callback) {
  // ...
  this.updater.enqueueSetState(this, partialState, callback, 'setState');
};
```

#### `forceUpdate()`

`forceUpdate()` 方法会使组件重新渲染，并跳过 `shouldComponentUpdate()`。这种情况很少需要，应尽量避免。它通常在组件深层的 state 在没有调用 `setState` 的情况下发生变化时使用。

```javascript
// 源自 src/ReactBaseClasses.js
Component.prototype.forceUpdate = function (callback) {
  this.updater.enqueueForceUpdate(this, callback, 'forceUpdate');
};
```

### `PureComponent`

`React.PureComponent` 与 `React.Component` 类似，但它通过对 props 和 state 进行浅层比较来实现 `shouldComponentUpdate()`。这可以作为一种简单的方式来优化某些组件的性能，防止不必要的重新渲染。

```javascript
// 源自 src/ReactBaseClasses.js
function PureComponent(props, context, updater) {
  // ... 初始化与 Component 类似
}

// ...
pureComponentPrototype.isPureReactComponent = true;
```

## Props (属性)

Props 是传入 React 组件的参数。它们是从父组件向子组件传递数据的主要方式。React 的一个关键原则是 **props 是只读的**。组件绝不能修改自身的 props。

### 传递和访问 Props

你可以将任何 JavaScript 值作为 prop 传递，包括字符串、数字、数组、对象和函数。

```javascript
function UserProfile(props) {
  return (
    <div>
      <h2>{props.user.name}</h2>
      <p>Age: {props.user.age}</p>
      <button onClick={props.onFollow}>Follow</button>
    </div>
  );
}

function App() {
  const userData = { name: 'John Doe', age: 30 };
  const handleFollow = () => console.log('Follow button clicked!');

  return <UserProfile user={userData} onFollow={handleFollow} />;
}
```

在此示例中，`App` 组件将一个对象 (`userData`) 和一个函数 (`handleFollow`) 作为 props 传递给 `UserProfile` 组件。

---

理解组件和 props 对于构建任何 React 应用程序都至关重要。有了这个基础，你就可以开始用简单、独立的部分来构建复杂的 UI。要学习如何编写这些示例中使用的类 HTML 语法，请继续阅读下一节关于 [JSX](./core-apis-jsx.md) 的内容。