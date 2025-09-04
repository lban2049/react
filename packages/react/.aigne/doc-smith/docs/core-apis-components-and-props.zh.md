# 组件与 Props

组件是 React 应用程序的基本构建块。它们允许你将用户界面拆分为独立的、可重用的部分，并对每个部分进行独立思考。Props（properties 的缩写）是组件从其父组件接收数据以配置其外观和行为的方式。

本节将介绍定义组件的两种主要方式——函数组件和类组件，并解释如何使用 props 在它们之间传递数据。

## 函数组件

函数组件是 React 中创建组件的现代化推荐方式。它们是简单的 JavaScript 函数，接受一个 `props` 对象作为参数，并返回一个待渲染的 React 元素。

```javascript
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

// 用法：
const element = <Welcome name="Sara" />;
```

最初，它们被称为“无状态组件”。然而，随着 Hooks 的引入，函数组件现在可以管理自身的状态并执行副作用。你可以在 [Hooks](./hooks.md) 一节中了解更多相关内容。

## 类组件

类组件是使用 ES6 类定义组件的另一种方式。要创建类组件，你需要继承 `React.Component` 或 `React.PureComponent` 基类。

```javascript
direction: down

Parent-Component: {
  shape: rectangle
  label: "父组件"
}

Child-Component: {
  shape: rectangle
  label: "子组件"
}

Props: {
  shape: parallelogram
  label: "Props 数据"
}

UI: {
  shape: document
  label: "渲染后的 UI"
}

Parent-Component -> Props: "传递"
Props -> Child-Component: "接收"
Child-Component -> UI: "渲染"
```

### `React.Component`

这是使用 ES6 类定义的 React 组件的主要基类。它要求你实现 `render()` 方法，该方法会返回一个 React 元素。

**Props 和 State**

与将 props 作为参数接收的函数组件不同，类组件通过 `this.props` 访问 props。它们还可以使用名为 `state` 的特殊属性来管理内部数据。

```javascript
class Counter extends React.Component {
  constructor(props) {
    super(props);
    // State 在构造函数中初始化
    this.state = { count: 0 };
  }

  // ... 更新 state 的方法 ...

  render() {
    return (
      <div>
        <p>你点击了 {this.state.count} 次</p>
        <button onClick={() => this.increment()}>点我</button>
      </div>
    );
  }
}
```

#### `setState(partialState, [callback])`

要修改组件的 state，必须使用 `setState()` 方法。你应该将 `this.state` 视为不可变数据。

- **异步更新**：`setState()` 会将对组件 state 的更改排入队列，并通知 React 该组件及其子组件需要使用更新后的 state 重新渲染。无法保证 `this.state` 会立即更新。
- **函数式更新**：如果你的下一个 state 依赖于前一个 state，你可以向 `setState` 传递一个函数。该函数将接收先前的 state 和 props 作为参数，并应返回一个要合并到 state 中的对象。

```javascript
// 源自 ReactBaseClasses.js - 简化示例

Component.prototype.setState = function (partialState, callback) {
  this.updater.enqueueSetState(this, partialState, callback, 'setState');
};

// 使用对象调用 setState 的示例
this.setState({ count: this.state.count + 1 });

// 使用函数调用 setState 的示例（对于依赖先前 state 的更新更安全）
this.setState((prevState, props) => ({
  count: prevState.count + props.increment
}));
```

#### `forceUpdate([callback])`

默认情况下，当组件的 state 或 props 发生变化时，组件会重新渲染。如果 `render()` 方法依赖于其他数据，可以通过调用 `forceUpdate()` 来通知 React 组件需要重新渲染。该方法应谨慎使用。

调用 `forceUpdate()` 将导致组件调用 `render()` 方法，并跳过 `shouldComponentUpdate()`。子组件仍会触发其正常的生命周期方法。

### `React.PureComponent`

`React.PureComponent` 是 `React.Component` 的一个变体。主要区别在于 `React.Component` 未实现 `shouldComponentUpdate()`，而 `React.PureComponent` 通过对 props 和 state 进行浅层比较实现了该方法。

如果组件的 `render()` 函数在 props 和 state 相同的情况下始终渲染相同的结果，那么在某些情况下可以使用 `PureComponent` 来提升性能。

```javascript
// 源自 ReactBaseClasses.js

function PureComponent(props, context, updater) {
  // ... 构造函数逻辑 ...
}

const pureComponentPrototype = (PureComponent.prototype = new ComponentDummy());
pureComponentPrototype.constructor = PureComponent;
assign(pureComponentPrototype, Component.prototype);
pureComponentPrototype.isPureReactComponent = true; // 此标志将其标识为 PureComponent
```

## Props 是只读的

无论你将组件声明为函数还是类，它都决不能修改自身的 props。所有 React 组件都必须像纯函数一样对待它们的 props。这可以确保 UI 的可预测性，也更易于调试。

```javascript
// 正确：一个不会改变其输入的纯函数。
function sum(a, b) {
  return a + b;
}

// 错误：此函数会改变其自身的输入。
function withdraw(account, amount) {
  account.total -= amount;
}
```

---

既然你已经了解了如何构建和组合组件，下一步就是掌握用于描述其 UI 的语法。请前往 [JSX](./core-apis-jsx.md) 一节了解更多内容。