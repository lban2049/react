# Refs

Refs 提供了一种访问在 render 方法中创建的 DOM 节点或 React 元素的方式。虽然通常你会让 React 处理所有的 DOM 操作，但有时你可能需要以命令式的方式修改典型数据流之外的子元素。Refs 是完成此操作的推荐方式。

Refs 的常见用例包括：
- 管理焦点、文本选择或媒体播放。
- 触发命令式动画。
- 与第三方 DOM 库集成。

避免将 refs 用于任何可以通过声明式方式完成的事情。例如，不要在 `Dialog` 组件上暴露 `open()` 和 `close()` 方法，而是向其传递一个 `isOpen` prop。

本节介绍了用于创建和转发 refs 的核心 API，这些 API 通常在类组件中使用。对于函数组件，[`useRef`](./hooks-ref.md) Hook 是现代的标准。

---

## `createRef`

`React.createRef()` 函数会创建一个 ref 对象，该对象可以通过 `ref` 属性附加到 React 元素上。该 ref 对象有一个可变的 `current` 属性，用于保存相应的 DOM 节点或组件实例。

从其实现中可以看出，它创建了一个简单的对象，其中包含一个初始化为 `null` 的 `current` 属性。

```javascript
// src/ReactCreateRef.js 的简化视图
export function createRef(): RefObject {
  const refObject = {
    current: null,
  };
  return refObject;
}
```

当 `ref` 属性用于 HTML 元素时，一旦组件挂载，`ref` 的 `current` 属性就会接收底层的 DOM 元素作为其值。当组件卸载时，React 会将其更新为 `null`。

### 在类组件中的用法

一个常见的模式是在类组件的构造函数中创建一个 ref，并将其附加到 `render` 方法中的一个元素上。

```javascript
import React, { Component, createRef } from 'react';

class MyComponent extends Component {
  constructor(props) {
    super(props);
    // 创建一个 ref 来存储 textInput DOM 元素
    this.textInput = createRef();
  }

  componentDidMount() {
    // 现在组件已经挂载，我们可以通过 'current' 属性访问 DOM 节点
    // 并让输入框获得焦点。
    if (this.textInput.current) {
      this.textInput.current.focus();
    }
  }

  render() {
    // 将 ref 附加到 <input> 元素。当此元素挂载到
    // DOM 中时，React 会将 DOM 节点赋值给 this.textInput.current。
    return <input type="text" ref={this.textInput} />;
  }
}
```
在此示例中，组件挂载后，`this.textInput.current` 将指向 `<input>` DOM 节点，从而允许我们调用其 `focus()` 方法。

---

## `forwardRef`

Ref 转发是一种将 ref 自动地通过组件传递到其子组件之一的技术。这对于可重用组件尤其有用，因为它允许父组件获取对子组件渲染树深处的 DOM 节点的引用。

`ref` 属性不是一个标准的 prop，它由 React 特别处理。如果你向自定义组件添加 `ref`，你将无法获得对其 DOM 节点的引用。`React.forwardRef` 通过创建一个可以接受 `ref` 并将其转发的组件来解决这个问题。

`forwardRef` 接受一个渲染函数，该函数接收 `props` 和 `ref` 作为参数。然后，你可以将 `ref` 参数转发给组件内部的一个元素。

```javascript
// src/ReactForwardRef.js 的简化视图
export function forwardRef<Props, ElementType: React$ElementType>(
  render: (
    props: Props,
    ref: React$RefSetter<React$ElementRef<ElementType>>,
  ) => React$Node,
) {
  // ... 内部逻辑 ...
  const elementType = {
    $$typeof: REACT_FORWARD_REF_TYPE,
    render,
  };
  return elementType;
}
```

### 示例

让我们创建一个 `FancyButton` 组件，它将其接收到的任何 refs 转发到底层的 DOM `<button>` 元素。

```javascript
import React, { forwardRef, createRef } from 'react';

// 创建一个将 ref 转发到 DOM 按钮的组件
const FancyButton = forwardRef((props, ref) => (
  <button ref={ref} className="FancyButton">
    {props.children}
  </button>
));

// 使用 FancyButton 的父组件
class App extends React.Component {
  constructor(props) {
    super(props);
    // 创建一个将指向按钮 DOM 元素的 ref
    this.buttonRef = createRef();
  }

  componentDidMount() {
    // ref 现在指向由 FancyButton 渲染的 <button> 元素
    if (this.buttonRef.current) {
      this.buttonRef.current.style.backgroundColor = 'lightblue';
      this.buttonRef.current.focus();
    }
  }

  render() {
    return (
      <FancyButton ref={this.buttonRef}>Click me!</FancyButton>
    );
  }
}
```
如果没有 `forwardRef`，`<FancyButton>` 上的 `ref` 将为 `null`。有了它，`App` 组件中的 `this.buttonRef.current` 会正确地指向 `<button>` DOM 节点，从而允许父组件直接与其交互。

---

### 后续步骤

对于某些命令式操作，Refs 是一个必要的工具，但应作为一种应急方案使用。理解它们的工作原理是掌握 React 组件模型的关键部分。

<x-cards>
  <x-card data-title="Ref Hooks" data-icon="lucide:hook" data-href="/hooks/ref">
    对于使用函数组件的现代 React 应用程序，请学习 `useRef` 和 `useImperativeHandle` Hooks，它们是管理 refs 的标准。
  </x-card>
  <x-card data-title="Context" data-icon="lucide:box" data-href="/core-apis/context">
    探索 Context，这是一种在组件树中传递数据的不同方式，无需在每一层手动传递 props。
  </x-card>
</x-cards>