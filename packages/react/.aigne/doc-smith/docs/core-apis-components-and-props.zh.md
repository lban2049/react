# 组件与 Props

组件是所有 React 应用的核心构建块。它们能让你将 UI 拆分为独立、可复用的部分，并对每个部分进行独立思考。从概念上讲，组件就像 JavaScript 函数。它们接受任意输入（称为“props”），并返回描述屏幕上应显示内容的 React 元素。

### 函数组件和类组件

React 提供了两种定义组件的主要方式：

1.  **函数组件**：编写组件最简单的方式。它们是接受单个 `props` 对象并返回一个 React 元素的 JavaScript 函数。
2.  **类组件**：这些是继承自 `React.Component` 的 ES6 类，提供了更多功能，如本地 state 和生命周期方法。随着 Hooks 的引入，你现在也可以在函数组件中使用这些功能。

下面是一个简单的 `Welcome` 组件，分别以函数和类的形式编写：

**函数组件**
```javascript
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```

**类组件**
```javascript
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

从 React 的角度来看，这两个组件是等价的。

### 组件基类

当你定义一个类组件时，需要继承 `React.Component`。这个基类提供了组件管理其 state 和生命周期的基本功能。

```javascript
// From src/ReactBaseClasses.js

function Component(props, context, updater) {
  this.props = props;
  this.context = context;
  this.refs = emptyObject;
  this.updater = updater || ReactNoopUpdateQueue;
}

Component.prototype.isReactComponent = {};
```

从构造函数中可以看出，每个类组件实例都有几个关键属性：
*   `this.props`: 包含从父组件传递给该组件的属性。
*   `this.context`: 一项高级功能，用于在组件树中传递数据，而无需在每个层级手动传递 props。更多详情请参阅 [Context](./core-apis-context.md) 文档。
*   `this.refs`: 一种访问在 render 方法中创建的 DOM 节点或 React 组件的方式。请参阅 [Refs](./core-apis-refs.md) 文档。
*   `this.updater`: 一个处理 state 更新队列和组件重新渲染的对象。

`isReactComponent` 属性是一个空对象，React 内部用它来区分普通 JavaScript 函数和类组件。

#### 更新 State

类组件有两种主要方法来触发 UI 更新。

1.  **`setState(partialState, callback)`**

    这是更新组件本地 state 的主要方法。它会告诉 React 该组件及其子组件需要使用更新后的 state 重新渲染。

    *   **异步性**：为了性能，React 可能会将多个 `setState()` 调用合并为一次更新。由于 `this.props` 和 `this.state` 可能是异步更新的，因此不应依赖它们的值来计算下一个 state。相反，应使用函数形式的 `setState`。
    *   **合并**：当你调用 `setState()` 时，React 会将你提供的对象合并到当前 state 中。

    ```javascript
    // 正确：使用函数访问先前的 state
    this.setState((prevState, props) => ({
      counter: prevState.counter + 1
    }));

    // 错误：由于异步更新，可能会导致 bug
    this.setState({
      counter: this.state.counter + 1
    });
    ```

2.  **`forceUpdate(callback)`**

    默认情况下，当组件的 state 或 props 发生变化时，组件会重新渲染。如果你的 `render()` 方法依赖于某些其他数据，你可以通过调用 `forceUpdate()` 来告诉 React 该组件需要重新渲染。调用 `forceUpdate()` 将导致组件调用 `render()` 方法，并跳过 `shouldComponentUpdate()`。应谨慎使用此方法。

### PureComponent

`React.PureComponent` 与 `React.Component` 类似，但它通过对 prop 和 state 进行浅层比较来实现 `shouldComponentUpdate()` 方法。如果新的 props 和 state 与之前的 props 和 state 浅层相等，则组件不会重新渲染，这在某些情况下可以提升性能。

```javascript
// From src/ReactBaseClasses.js
function PureComponent(props, context, updater) {
  this.props = props;
  this.context = context;
  this.refs = emptyObject;
  this.updater = updater || ReactNoopUpdateQueue;
}

const pureComponentPrototype = (PureComponent.prototype = new ComponentDummy());
pureComponentPrototype.constructor = PureComponent;
assign(pureComponentPrototype, Component.prototype);
pureComponentPrototype.isPureReactComponent = true;
```

### Props 是只读的

无论你将组件声明为函数还是类，它都绝不能修改自身的 props。所有 React 组件都必须像纯函数一样对待它们的 props。

*   **正确**：一个不会改变其输入的纯函数。
    ```javascript
    function sum(a, b) {
      return a + b;
    }
    ```

*   **错误**：一个会改变其自身输入的不纯函数。
    ```javascript
    function withdraw(account, amount) {
      account.total -= amount; // 不要这样做！
    }
    ```

### 后续步骤

现在你已经了解了组件和 props 的基础知识，可以探索如何使用更简洁的语法来编写它们。

- 了解 [JSX](./core-apis-jsx.md)，将类似 XML 的语法直接嵌入到 JavaScript 代码中。
- 深入研究 [Hooks](./hooks.md) 文档，学习如何在函数组件中使用 state 和其他 React 功能。
