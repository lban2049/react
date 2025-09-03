# Ref Hooks

Ref Hooks 提供了一种访问 DOM 节点或在多次渲染之间持久化可变值而无需触发重新渲染的方法。它们对于管理不直接与组件视觉输出相关的值至关重要，例如管理焦点、媒体播放或与第三方 DOM 库集成。本节将介绍 `useRef` 和 `useImperativeHandle`。

如需管理触发重新渲染的组件状态，请参阅 [State Hooks](./hooks-state.md)。

## `useRef`

`useRef` Hook 返回一个可变的 ref 对象，其 `.current` 属性被初始化为传入的参数 (`initialValue`)。返回的对象将在组件的整个生命周期内保持不变。一个常见的用例是直接访问 DOM 元素。

更改 ref 的 `.current` 属性**不会**导致重新渲染。

### 语法

```javascript
function useRef<T>(initialValue: T): {current: T}
```

### 参数

| Name | Type | Description |
|---|---|---|
| `initialValue` | `T` | `ref` 对象 `current` 属性的初始值。它可以是任何类型的值。 |

### 返回值

`useRef` 返回一个具有 `current` 属性的单一可变对象。

### 示例：访问 DOM 元素

以下示例展示了如何使用 `useRef` 存储对 input DOM 元素的引用，并在按钮被点击时使其聚焦。

```javascript
import { useRef } from 'react';

function TextInputWithFocusButton() {
  const inputEl = useRef(null);
  
  const onButtonClick = () => {
    // `.current` 指向已挂载的文本输入元素
    if (inputEl.current) {
      inputEl.current.focus();
    }
  };

  return (
    <>
      <input ref={inputEl} type="text" />
      <button onClick={onButtonClick}>Focus the input</button>
    </>
  );
}
```

在此示例中，`inputEl` 保存了对 `<input>` 元素的引用。当按钮被点击时，我们调用 `inputEl.current` 的 `focus()` 方法。

## `useImperativeHandle`

`useImperativeHandle` 可以在使用 `ref` 时，自定义暴露给父组件的实例值。当您希望控制父组件能够访问子组件实例上的哪些内容时，这个 Hook 非常有用。它应始终与 `forwardRef` 结合使用。

这个 Hook 允许您向父组件暴露一个特定的、有限的 API，而不是整个组件实例，这有助于创建更可预测和解耦的组件。

### 语法

```javascript
function useImperativeHandle<T>(
  ref: {current: T | null} | ((inst: T | null) => mixed) | null | void,
  create: () => T,
  deps: Array<mixed> | void | null,
): void
```

### 参数

| Name | Type | Description |
|---|---|---|
| `ref` | `RefObject` | 通过 `forwardRef` 从父组件转发的 `ref`。 |
| `create` | `() => T` | 一个函数，其返回值将作为 ref 的 `current` 值暴露出来。这个值可以是一个包含您想要暴露的方法或属性的对象。 |
| `deps` | `Array<any>?` | 一个可选的依赖项数组。每当此数组中的任何依赖项发生更改时，都会重新执行 `create` 函数。如果省略，则在每次渲染时都会运行。 |


### 示例：暴露自定义的 Focus 方法

在此示例中，父组件需要聚焦子组件内部的输入框。子组件 `FancyInput` 使用 `useImperativeHandle` 仅暴露一个 `focus` 方法。

```javascript
import React, { useRef, useImperativeHandle, forwardRef } from 'react';

// 使用 forwardRef 和 useImperativeHandle 的子组件
const FancyInput = forwardRef((props, ref) => {
  const inputRef = useRef();
  
  useImperativeHandle(ref, () => ({
    // 向父组件暴露一个自定义的 'focus' 方法
    focus: () => {
      if (inputRef.current) {
        inputRef.current.focus();
      }
    },
    // 你也可以在这里暴露其他方法
    clear: () => {
      if (inputRef.current) {
        inputRef.current.value = '';
      }
    }
  }));

  return <input ref={inputRef} type="text" placeholder="Enter text..." />;
});

// 父组件
function App() {
  const fancyInputRef = useRef(null);

  const handleFocusClick = () => {
    if (fancyInputRef.current) {
      fancyInputRef.current.focus();
    }
  };

  return (
    <div>
      <FancyInput ref={fancyInputRef} />
      <button onClick={handleFocusClick}>
        Focus Child Input
      </button>
    </div>
  );
}
```

父组件现在可以调用 `fancyInputRef.current.focus()`，但不能直接访问底层的 `<input>` DOM 节点或 `FancyInput` 的其他内部属性。

---

通过使用 `useRef` 和 `useImperativeHandle`，您可以有效地管理超出 React 标准声明式数据流的交互。

接下来，学习如何使用 [Performance Hooks](./hooks-performance.md) 优化应用程序的渲染性能。