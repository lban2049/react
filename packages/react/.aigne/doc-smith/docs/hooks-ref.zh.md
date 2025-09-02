# Ref Hooks

Ref Hooks 提供了一种直接访问 DOM 节点或在多次渲染之间持久化可变值而无需触发重新渲染的方法。它们对于与第三方 DOM 库集成、管理焦点以及处理不直接参与组件视觉输出的值至关重要。

若想对 ref 有一个基础的了解，你可能需要先查阅 [Refs](./core-apis-refs.md) 文档。

本节涵盖以下 hooks：
- `useRef`：创建一个可变 ref 对象，可以持有一个值或一个 DOM 节点。
- `useImperativeHandle`：在使用 `ref` 时，自定义暴露给父组件的实例值。

---

## useRef

`useRef` Hook 返回一个可变的 ref 对象，其 `.current` 属性被初始化为传入的参数（`initialValue`）。返回的对象在组件的整个生命周期内保持不变。

`useRef` 的一个常见用例是直接访问 DOM 元素。它也可以用来持有任何可变值，类似于在类中使用实例字段的方式。

```d2
direction: down

Component: {
  "useRef(initialValue)": {
    style.bold: true
  }

  "Ref Object": {
    shape: cylinder
    ".current": "initialValue"
  }

  "Component Lifetime": {
    shape: sequence_diagram
    A: "Mount"
    B: "Re-render"
    C: "Re-render"
    D: "Unmount"

    A -> B -> C -> D
  }

  "useRef(initialValue)" -> "Ref Object": "Returns"
  "Ref Object" -> "Component Lifetime": "Persists across renders"
}
```

### 语法

```javascript
export function useRef<T>(initialValue: T): {current: T}
```

### 参数

| Parameter | Type | Description |
| --- | --- | --- |
| `initialValue` | `T` | ref 对象的 `current` 属性的初始值。 |

### 返回值

`useRef` 返回一个带有 `current` 属性的可变对象。最初，`ref.current` 被设置为 `initialValue`。之后你可以更改 `ref.current` 的值。如果你将 ref 对象作为 JSX 节点上的 `ref` 属性传递给 React（例如 `<div ref={myRef} />`），React 会将其 `current` 属性设置为相应的 DOM 节点。

### 示例 1：访问 DOM 元素

你可以使用 ref 直接操作 DOM 元素，例如管理焦点、文本选择或媒体播放。

```javascript
import React, { useRef } from 'react';

function TextInputWithFocusButton() {
  const inputEl = useRef(null);

  const onButtonClick = () => {
    // `current` 指向已挂载的文本输入元素
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

在此示例中，`useRef` 创建了一个 ref 对象。我们通过传递 `<input ref={inputEl}>` 将其附加到 `<input>` 元素上。当按钮被点击时，`inputEl.current.focus()` 会被调用，从而以编程方式使输入框获得焦点。

### 示例 2：存储可变值

`useRef` 对于保存任何可变值也很有用。这对于那些你希望在多次渲染之间保持不变，但又不想在它们改变时触发重新渲染的值来说非常方便。

```javascript
import React, { useState, useEffect, useRef } from 'react';

function Timer() {
  const [count, setCount] = useState(0);
  const intervalRef = useRef(null);

  useEffect(() => {
    intervalRef.current = setInterval(() => {
      setCount(prevCount => prevCount + 1);
    }, 1000);

    // 清理函数
    return () => {
      clearInterval(intervalRef.current);
    };
  }, []); // 空依赖数组意味着此 effect 仅在挂载时运行一次

  return (
    <div>
      <h1>Count: {count}</h1>
      <button onClick={() => clearInterval(intervalRef.current)}>
        Stop Timer
      </button>
    </div>
  );
}
```

在这里，`useRef` 存储了 interval ID。我们可以在组件的任何地方访问和清除此 interval，而不会导致重新渲染，因为更改 `intervalRef.current` 不会触发组件更新。

---

## useImperativeHandle

`useImperativeHandle` 在使用 `ref` 时自定义暴露给父组件的实例值。它应始终与 `forwardRef` 一起使用。这允许子组件向其父组件暴露特定的函数，而不是暴露整个组件实例，从而提供了更好的封装性。

### 语法

```javascript
export function useImperativeHandle<T>(
  ref: {current: T | null} | ((inst: T | null) => mixed) | null | void,
  create: () => T,
  deps: Array<mixed> | void | null,
): void
```

### 参数

| Parameter | Type | Description |
| --- | --- | --- |
| `ref` | `Ref<T>` | 从父组件转发的 ref。 |
| `create` | `() => T` | 一个返回自定义句柄的函数。此句柄将被赋值给父组件中的 `ref.current` 属性。 |
| `deps` | `Array<any>` | 可选的依赖数组。每当其中一个依赖项发生变化时，`create` 函数就会重新执行。 |

### 示例

在此示例中，父组件需要在一个自定义输入组件上调用 `focusAndClear` 方法。

```javascript
import React, { useRef, useImperativeHandle, forwardRef, useState } from 'react';

// 子组件
const CustomInput = forwardRef((props, ref) => {
  const [value, setValue] = useState('');
  const inputRef = useRef();

  useImperativeHandle(ref, () => ({
    focusAndClear: () => {
      inputRef.current.focus();
      setValue('');
    },
    // 你可以在这里暴露其他方法
  }));

  return (
    <input
      ref={inputRef}
      value={value}
      onChange={(e) => setValue(e.target.value)}
      placeholder="Type something..."
    />
  );
});

// 父组件
function ParentComponent() {
  const customInputRef = useRef(null);

  const handleFocusAndClear = () => {
    if (customInputRef.current) {
      customInputRef.current.focusAndClear();
    }
  };

  return (
    <div>
      <CustomInput ref={customInputRef} />
      <button onClick={handleFocusAndClear}>Focus and Clear Input</button>
    </div>
  );
}
```

`ParentComponent` 创建一个 ref (`customInputRef`) 并将其传递给 `CustomInput`。被 `forwardRef` 包裹的 `CustomInput` 组件使用 `useImperativeHandle` 来精确定义 `customInputRef.current` 上可访问的属性和方法。然后，父组件可以调用 `customInputRef.current.focusAndClear()` 以受控的方式与子组件进行交互。

---

通过 Ref Hooks，你可以精确控制不影响渲染的 DOM 元素和可变状态。

接下来，让我们探讨如何使用 [Performance Hooks](./hooks-performance.md) 来优化你的应用程序。