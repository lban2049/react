# 组件与 Props

组件是任何 React 应用的核心构建模块。它们允许你将 UI 拆分为独立、可复用的部分，并对每个部分进行独立思考。从概念上讲，组件就像 JavaScript 函数。它们接受任意输入（称为“props”），并返回描述屏幕上应显示内容的 React 元素。

### 函数组件与类组件

定义组件最简单的方式是编写一个 JavaScript 函数：

```javascript Function Component Example icon=logos:javascript
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```

这是一个**函数组件**。它接受一个包含数据的“props”（即 properties）对象作为参数，并返回一个 React 元素。我们建议在所有新代码中使用函数组件。

你也可以使用 ES6 class 来定义一个组件：

```javascript Class Component Example icon=logos:javascript
import React, { Component } from 'react';

class Welcome extends Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

这是一个**类组件**。它继承自 `React.Component`，并且必须实现一个 `render()` 方法，该方法返回一个 React 元素。数据通过 `this.props` 访问。

### `Component` 基类

当你定义一个类组件时，它会继承自 `React.Component` 基类。这个基类为组件管理其 state 和生命周期提供了基本的功能。

`React.Component` 提供的关键特性包括：

*   **`this.props`**：包含从父组件传递给该组件的属性。
*   **`this.context`**：持有来自最近的 Context provider 的 context 值。
*   **`this.refs`**：一个用于持有对 DOM 节点或其他 React 组件引用的对象。
*   **`this.updater`**：一个包含将 state 更新加入队列的方法的对象。

该基类还定义了两种请求重新渲染的主要方法：

1.  **`setState(partialState, callback)`**：这是用于更新组件本地 state 的主要方法。它将 `partialState` 合并到当前 state 中，并触发重新渲染。为了性能，React 可能会批量处理多个 `setState()` 调用，因此不保证更新是同步的。

    ```javascript setState Method icon=logos:javascript
    // 来自 React 的 Component 基类
    Component.prototype.setState = function (partialState, callback) {
      // ... 验证逻辑 ...
      this.updater.enqueueSetState(this, partialState, callback, 'setState');
    };
    ```

2.  **`forceUpdate(callback)`**：此方法会强制组件重新渲染，并跳过 `shouldComponentUpdate()` 检查。应谨慎使用此方法，通常用于当你确定组件 state 的某个深层部分在没有调用 `setState()` 的情况下发生了变化时。

    ```javascript forceUpdate Method icon=logos:javascript
    // 来自 React 的 Component 基类
    Component.prototype.forceUpdate = function (callback) {
      this.updater.enqueueForceUpdate(this, callback, 'forceUpdate');
    };
    ```

### `PureComponent`

`React.PureComponent` 是 `React.Component` 的一个特殊版本。唯一的区别是它在其 `shouldComponentUpdate()` 生命周期方法中实现了对 props 和 state 的浅层比较。在某些情况下，当 props 和 state 没有变化时，这可以通过防止不必要的重新渲染来提升性能。

```javascript PureComponent Definition icon=logos:javascript
// PureComponent 继承自一个共享 Component 原型的虚拟构造函数
const pureComponentPrototype = (PureComponent.prototype = new ComponentDummy());
pureComponentPrototype.constructor = PureComponent;
// 避免这些方法产生额外的原型跳转。
assign(pureComponentPrototype, Component.prototype);
pureComponentPrototype.isPureReactComponent = true;
```

当你的组件的渲染输出仅依赖于其 props 和 state，并且两者都是简单的数据结构时，应使用 `PureComponent`。

### 组合组件

组件可以在其输出中引用其他组件。这使我们能够对任何层级的细节都使用相同的组件抽象。在 React 应用中，按钮、表单、对话框、屏幕等，通常都以组件的形式来表示。

例如，我们可以创建一个多次渲染 `Welcome` 的 `App` 组件：

```javascript Composing Components icon=logos:javascript
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

function App() {
  return (
    <div>
      <Welcome name="Sara" />
      <Welcome name="Cahal" />
      <Welcome name="Edite" />
    </div>
  );
}
```

### Props 是只读的

无论你将组件声明为函数还是类，它都绝不能修改自身的 props。思考一下这个 `sum` 函数：

```javascript
function sum(a, b) {
  return a + b;
}
```

这类函数被称为“纯函数”，因为它们不会尝试改变其输入，并且对于相同的输入，总是返回相同的结果。相比之下，下面这个函数就是非纯函数，因为它改变了自身的输入：

```javascript
function withdraw(account, amount) {
  account.total -= amount; // 不要这样做！
}
```

React 非常灵活，但它有一条严格的规则：

**所有 React 组件都必须像纯函数一样对待它们的 props。**

当然，应用的 UI 是动态的，会随时间变化。在接下来的章节中，你将学习“state”，它允许 React 组件在不违反此规则的情况下，响应用户操作、网络响应以及任何其他事情来改变其输出。

---

既然你已经了解了组件和 props 的基础知识，下一步就是学习用于定义它们的语法。请继续阅读 [JSX](./core-apis-jsx.md) 章节以了解更多信息。