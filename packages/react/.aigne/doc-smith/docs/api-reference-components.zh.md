# 组件和类

本节为 React 应用的基础构建块提供了全面的参考：基类（`Component`、`PureComponent`）和内置组件（`Fragment`、`Suspense` 等）。这些对于创建简单和复杂的用户界面都至关重要。

要使用函数式组件构建现代 React 应用，请参阅 [Hooks](./api-reference-hooks.md) 文档。

---

## React.Component

当使用 ES6 类定义 React 组件时，`React.Component` 是其基类。它为组件生命周期、状态管理和事件处理提供了核心功能。

```javascript Component Example icon=logos:javascript
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

### 实例方法

#### `setState()`

`setState()` 会将组件状态的更改排入队列，并通知 React 该组件及其子组件需要使用更新后的状态重新渲染。这是用于响应事件处理器和服务器响应来更新用户界面的主要方法。

为了性能，React 可能会将多个 `setState()` 调用合并为一次更新。由于 `this.props` 和 `this.state` 可能是异步更新的，因此不应依赖它们的值来计算下一个状态。

**Parameters**

<x-field data-name="partialState" data-type="object | function" data-required="true" data-desc="一个包含状态变化的对象，或一个返回包含状态变化对象的函数。该函数接收先前的状态和当前的 props 作为参数：(prevState, props) => newState。"></x-field>
<x-field data-name="callback" data-type="function" data-required="false" data-desc="一个可选函数，将在状态更新且组件重新渲染后执行。"></x-field>

**Examples**

使用对象：
```javascript setState with Object icon=logos:javascript
this.setState({ quantity: this.state.quantity + 1 });
```

当新状态依赖于先前状态时，使用函数更安全：
```javascript setState with Function icon=logos:javascript
this.setState((prevState, props) => {
  return { counter: prevState.counter + props.increment };
});
```

#### `forceUpdate()`

默认情况下，当组件的状态或 props 发生变化时，组件将重新渲染。如果 `render()` 方法依赖于其他数据，可以通过调用 `forceUpdate()` 来告知 React 该组件需要重新渲染。调用 `forceUpdate()` 将导致在组件上调用 `render()`，并跳过 `shouldComponentUpdate()`。

**Parameters**

<x-field data-name="callback" data-type="function" data-required="false" data-desc="一个可选函数，将在更新完成后执行。"></x-field>


### 实例属性

<x-field data-name="props" data-type="object" data-desc="传递给组件的 props。这些是只读的。"></x-field>
<x-field data-name="state" data-type="object" data-desc="组件的本地状态。它应该是一个对象，并且可以使用 setState() 进行更改。"></x-field>
<x-field data-name="context" data-type="object" data-desc="组件的 context 值，如果它订阅了某个 context。"></x-field>
<x-field data-name="refs" data-type="object" data-desc="一个包含对 DOM 节点或 React 组件引用的对象。"></x-field>

### 已弃用的 API

以下 API 已被弃用，并将在开发模式下显示警告。应避免使用它们。

| API | Alternative | Reason for Deprecation |
|---|---|---|
| `isMounted()` | 通过在 `componentDidMount` 和 `componentWillUnmount` 中的属性检查挂载状态。 | `isMounted()` 是一种反模式。确保在 `componentWillUnmount` 中进行清理，以防止内存泄漏。 |
| `replaceState()` | 改用 `setState()`。 | 移除了 `replaceState()` 以鼓励更可预测的状态更新模型。详情请参阅 [GitHub issue #3236](https://github.com/facebook/react/issues/3236)。 |

---

## React.PureComponent

`React.PureComponent` 与 `React.Component` 类似。它们之间的区别在于 `React.Component` 没有实现 `shouldComponentUpdate()`，而 `React.PureComponent` 通过对 prop 和 state 的浅层比较实现了它。

如果 React 组件的 `render()` 函数在给定相同的 props 和 state 时渲染出相同的结果，那么在某些情况下，使用 `React.PureComponent` 可以提升性能。

```javascript PureComponent Example icon=logos:javascript
class Greeting extends React.PureComponent {
  render() {
    return <h1>Hello, {this.props.name}!</h1>;
  }
}
```
`PureComponent` 的 `shouldComponentUpdate()` 只会浅层比较对象。如果这些对象包含复杂的数据结构，对于更深层次的差异，它可能会产生假阴性（false-negatives）。

---

## 内置组件

React 提供了一些可以在 JSX 中使用的内置组件。

### Fragment

`React.Fragment` 允许你将一组子元素分组，而无需向 DOM 添加额外的节点。当一个组件需要返回多个元素时，这非常有用。

```javascript React.Fragment Example icon=logos:javascript
function UserInfo() {
  return (
    <React.Fragment>
      <dt>Name</dt>
      <dd>John Doe</dd>
      <dt>Email</dt>
      <dd>john.doe@example.com</dd>
    </React.Fragment>
  );
}
```

还有一种更短的语法 `<>...</>`。但是，这种简写语法不接受 key 或属性。

```javascript Fragment Shorthand icon=logos:javascript
function UserInfo() {
  return (
    <>
      <dt>Name</dt>
      <dd>John Doe</dd>
    </>
  );
}
```

### Suspense

`React.Suspense` 允许你在组件等待加载某些内容时显示一个备用 UI。它通常与 `React.lazy` 一起用于代码分割。

**Props**

<x-field data-name="fallback" data-type="ReactNode" data-required="true" data-desc="一个在子组件挂起时渲染的 React 元素（例如，一个加载指示器）。"></x-field>

```javascript Suspense Example icon=logos:javascript
const OtherComponent = React.lazy(() => import('./OtherComponent'));

function MyComponent() {
  return (
    <div>
      <Suspense fallback={<div>加载中...</div>}>
        <OtherComponent />
      </Suspense>
    </div>
  );
}
```

### Profiler

`React.Profiler` 用于测量 React 应用的渲染频率以及渲染的“成本”。其目的是帮助识别应用中速度较慢且可能受益于备忘录（memoization）等优化的部分。

**Props**

<x-field data-name="id" data-type="string" data-required="true" data-desc="Profiler 的唯一标识符，用于识别正在测量的树的哪一部分。"></x-field>
<x-field data-name="onRender" data-type="function" data-required="true" data-desc="一个回调函数，React 会在被分析的树中的组件每次提交更新时调用该函数。"></x-field>

```javascript Profiler Example icon=logos:javascript
function onRenderCallback(
  id, // 刚刚提交的 Profiler 树的 “id” prop
  phase, // “mount”（如果树刚刚挂载）或 “update”（如果它重新渲染）
  actualDuration, // 渲染已提交更新所花费的时间
  baseDuration, // 在没有备忘录的情况下渲染整个子树的估计时间
  startTime, // React 开始渲染此更新的时间
  commitTime, // React 提交此更新的时间
  interactions // 属于此更新的交互集合
) {
  // 汇总或记录渲染计时...
}

function App() {
  return (
    <Profiler id="Application" onRender={onRenderCallback}>
      <MyComponent />
    </Profiler>
  );
}
```

### StrictMode

`React.StrictMode` 是一个用于突出显示应用中潜在问题的工具。与 `Fragment` 类似，`StrictMode` 不会渲染任何可见的 UI。它会为其后代激活额外的检查和警告。这些检查仅在开发模式下运行；它们不会影响生产构建。

```javascript StrictMode Example icon=logos:javascript
import React from 'react';

function App() {
  return (
    <React.StrictMode>
      <div>
        <Header />
        <MainContent />
      </div>
    </React.StrictMode>
  );
}
```