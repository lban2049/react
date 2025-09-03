# Ref

Ref 提供了一种访问在渲染方法中创建的 DOM 节点或 React 元素的方法。当你需要管理焦点、触发动画或与第三方 DOM 库集成时，Ref 非常有用——这些操作超出了 React 的典型数据流范围。

虽然功能强大，但应谨慎使用 ref，因为它们会破坏自上而下的数据流，使代码更难理解。在使用 ref 之前，请考虑是否可以通过 state 和 props 实现所需的行为。

本指南涵盖了 ref 的核心 API：`createRef` 和 `forwardRef`。对于现代函数组件，推荐使用 `useRef` Hook。更多详情请参阅 [Ref Hooks](./hooks-ref.md) 指南。

## createRef

`createRef` 函数创建一个 ref 对象。它的主要特性是一个 `.current` 属性，React 会用相应的 DOM 元素或类组件实例来填充它。

`createRef` 最常用于类组件。

### 语法

```javascript
const refObject = React.createRef();
```

该函数返回一个包含单个可变属性 `current` 的 ref 对象。最初，`refObject.current` 为 `null`。当 ref 在 `render` 方法中附加到一个元素时，React 会将该 DOM 元素赋值给 `current` 属性。

### 示例：访问 DOM 节点

这是一个典型的用例：在组件挂载时自动聚焦输入字段。

```javascript
import React, { Component, createRef } from 'react';

class MyInput extends Component {
  constructor(props) {
    super(props);
    // 1. 创建一个 ref 来存储 textInput DOM 元素
    this.textInput = createRef();
  }

  componentDidMount() {
    // 3. 通过 .current 属性访问 DOM 节点并调用 focus()
    this.textInput.current.focus();
  }

  render() {
    // 2. 将 ref 附加到 <input> 元素
    return <input type="text" ref={this.textInput} />;
  }
}
```

在此示例中：
1.  我们在构造函数中使用 `createRef()` 创建了一个名为 `this.textInput` 的 ref。
2.  我们通过将其传递给 `ref` 属性，在 `render` 方法中将此 ref 附加到 `<input>` 元素。
3.  组件挂载后，React 会更新 `this.textInput` 的 `current` 属性。在 `componentDidMount` 中，我们就可以直接访问输入框的 DOM 节点并调用其 `focus()` 方法。

## forwardRef

默认情况下，你不能将 `ref` prop 传递给函数组件，因为它们没有实例。Ref 转发是一项功能，它允许组件接收一个 ref 并将其向下传递给子组件。

这对于创建可重用组件（如样式化按钮或输入字段）特别有用，因为父组件可能需要直接访问底层的 DOM 节点。

### 语法

```javascript
const MyComponent = React.forwardRef((props, ref) => {
  // 使用 props 和 ref 的渲染逻辑
});
```

`React.forwardRef` 接受一个渲染函数，该函数接收 `props` 和 `ref` 作为参数。然后，你可以将接收到的 `ref`“转发”给组件内部的一个元素。

### 示例：将 Ref 转发到 DOM 元素

让我们创建一个 `FancyButton` 组件，它将其接收到的任何 ref 转发给底层的 DOM `<button>` 元素。

```javascript
import React, { createRef, forwardRef } from 'react';

// 1. 使用 forwardRef 创建一个组件
const FancyButton = forwardRef((props, ref) => (
  <button ref={ref} className="FancyButton">
    {props.children}
  </button>
));

function App() {
  // 2. 创建一个 ref 以附加到 DOM 按钮
  const buttonRef = createRef();

  const handleClick = () => {
    // 4. 访问按钮的 DOM 节点以聚焦它
    buttonRef.current.focus();
  };

  return (
    <>
      {/* 3. 将 ref 向下传递给 FancyButton */}
      <FancyButton ref={buttonRef}>Click me!</FancyButton>
      <button onClick={handleClick}>Focus Fancy Button</button>
    </>
  );
}
```

流程如下：
1.  我们用 `forwardRef` 包装 `FancyButton` 组件。
2.  `forwardRef` 将父组件传递的 `ref` 作为第二个参数提供给我们的渲染函数。
3.  我们将此 `ref` 传递或“转发”给 `<button>` 元素。
4.  这使得父组件 `App` 可以创建一个 `buttonRef`，将其传递给 `<FancyButton>`，并直接访问底层的 `<button>` DOM 节点。

### Ref 转发流程

下图说明了 ref 如何从父组件传递，经过一个转发组件，并附加到一个 DOM 节点上。

```d2
direction: down

"父组件": {
  shape: rectangle
  "const myRef = createRef()"

  "render()": {
    "<FancyButton ref={myRef} />"
  }
}

"FancyButton = forwardRef((props, ref) => ...)": {
  shape: package
  "render()": {
    "<button ref={ref} />"
  }
}

"DOM": {
  shape: cylinder
  "<button>"
}

"父组件" -> "FancyButton = forwardRef((props, ref) => ...)": "1. 传递 ref"
"FancyButton = forwardRef((props, ref) => ...)" -> "DOM": "2. 将 ref 转发到 <button>"
"父组件" -> "DOM": "3. myRef.current 现在指向 <button> 节点" {
  style.stroke-dash: 4
}
```

---

这涵盖了创建和转发 ref 的基本 API。虽然对于某些用例（尤其是在类组件和组件库中）至关重要，但现代 React 应用程序通常依赖于 Hook。

要了解现代方法，请继续阅读 [Ref Hooks](./hooks-ref.md) 指南。