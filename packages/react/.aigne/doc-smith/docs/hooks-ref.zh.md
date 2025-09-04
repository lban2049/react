# Ref Hook

Ref Hook 提供了一种直接访问 DOM 节点或创建一个在多次渲染之间保持不变且其本身不会导致重新渲染的值的引用的方法。它们对于管理焦点、媒体播放或与第三方 DOM 库集成至关重要。

本节介绍用于创建通用 ref 的 `useRef` 和用于自定义组件所暴露的 ref 的 `useImperativeHandle`。要对 ref 有一个基础的了解，你可能需要先查阅 [Ref](./core-apis-refs.md) 文档。

## `useRef`

`useRef` Hook 返回一个可变的 ref 对象，其 `.current` 属性被初始化为传入的参数（`initialValue`）。返回的对象在组件的整个生命周期内保持不变。

`useRef` 有两个主要用例：
1.  访问 DOM 元素。
2.  持有一个在变化时不会触发重新渲染的可变值。

**签名**

```typescript
function useRef<T>(initialValue: T): {current: T};
```

**参数**

| Name | Type | Description |
| --- | --- | --- |
| `initialValue` | `T` | ref 的 `current` 属性的初始值。它仅在初始渲染时使用。 |

**返回**

一个具有单一属性的可变 ref 对象：

| Name | Type | Description |
| --- | --- | --- |
| `current` | `T` | 初始值设为 `initialValue`。你可以直接修改此属性。改变它不会导致组件重新渲染。 |

### 示例：访问 DOM 元素

这是 `useRef` 最常见的用例。你可以将 ref 对象传递给 JSX 元素的 `ref` 属性，以获取对其底层 DOM 节点的直接引用。

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

在此示例中，当 React 挂载 `<input>` 元素后，`inputEl.current` 将是该元素的 DOM 节点。点击按钮会调用该 DOM 节点的 `focus()` 方法。

### 示例：存储可变值

你也可以使用 `useRef` 来存储任何可变值，类似于类中的实例属性。这对于那些希望在多次渲染之间保持不变，但其变化又不会触发重新渲染的值（例如定时器 ID）非常有用。

```javascript
import React, { useRef, useEffect } from 'react';

function Timer() {
  const intervalRef = useRef(null);

  useEffect(() => {
    intervalRef.current = setInterval(() => {
      console.log('Timer tick');
    }, 1000);

    // 清理函数
    return () => {
      if (intervalRef.current) {
        clearInterval(intervalRef.current);
      }
    };
  }, []); // 空依赖数组表示此 effect 仅在挂载时运行一次

  return <div>Timer is running (check console)</div>;
}
```

在这里，`intervalRef` 持有 `setInterval` 返回的 ID。我们可以在清理函数中访问它，以便在组件卸载时清除定时器，而当 `intervalRef.current` 被赋值时不会引起任何重新渲染。

## `useImperativeHandle`

`useImperativeHandle` Hook 用于在使用 `ref` 时，自定义暴露给父组件的实例值。它应与 `forwardRef` 结合使用。

`useImperativeHandle` 允许你暴露一个特定的、有限的命令式方法集，而不是暴露整个组件实例。这有助于避免破坏封装性，因为它阻止了父组件依赖于子组件的内部 DOM 结构。

**签名**

```typescript
function useImperativeHandle<T>(
  ref: {current: T | null} | ((inst: T | null) => mixed) | null | void,
  create: () => T,
  deps: Array<mixed> | void | null,
): void;
```

**参数**

| Name | Type | Description |
| --- | --- | --- |
| `ref` | `Ref<T>` | 通过 `forwardRef` 从父组件转发的 `ref`。 |
| `create` | `() => T` | 一个返回要暴露的值的函数。该值将被设置为父组件 ref 的 `current` 值。 |
| `deps` | `Array<mixed>` | 一个可选的依赖数组。每当此数组中的值发生变化时，`create` 函数将被重新执行。 |

### 示例：暴露一个自定义的 `focus` 方法

下面是一个 `FancyInput` 组件的示例，它使用 `useImperativeHandle` 仅向其父组件暴露一个 `focus` 方法。

```javascript
import React, { useRef, useImperativeHandle, forwardRef } from 'react';

// 子组件
const FancyInput = forwardRef((props, ref) => {
  const inputRef = useRef();
  
  useImperativeHandle(ref, () => ({
    // 暴露一个自定义的 `focus` 方法
    focus: () => {
      if (inputRef.current) {
        inputRef.current.focus();
        console.log('Child input focused imperatively!');
      }
    }
  }));

  return <input ref={inputRef} placeholder="I am a fancy input" />;
});

// 父组件
function App() {
  const fancyInputRef = useRef();

  const handleClick = () => {
    if (fancyInputRef.current) {
      fancyInputRef.current.focus();
    }
  };

  return (
    <div>
      <FancyInput ref={fancyInputRef} />
      <button onClick={handleClick}>
        Focus Child Input
      </button>
    </div>
  );
}
```

`App` 组件现在可以调用 `fancyInputRef.current.focus()`，但它不能直接访问底层的 `<input>` DOM 节点，从而保留了 `FancyInput` 的封装性。

### 父组件和子组件的 Ref 如何交互

下图说明了当父组件在使用了 `useImperativeHandle` 的子组件上调用命令式方法时的控制流程。

```d2
direction: down

Parent-Component: {
  shape: rectangle
  
  Child-Ref: {
    label: "const childRef = useRef()"
    shape: oval
  }
  
  Rendered-Child: {
    label: "<FancyInput ref={childRef} />"
    shape: rectangle
  }

  Button: {
    label: "<button onClick={() => childRef.current.focus()} />"
    shape: rectangle
  }
}

FancyInput-Component: {
  label: "FancyInput (由 forwardRef 包装)"
  shape: package

  useImperativeHandle: {
    label: "useImperativeHandle(ref, () => ({ focus: ... }))"
    shape: hexagon
  }

  Internal-Input: {
    label: "<input ref={internalInputRef} />"
    shape: rectangle
  }
}

Parent-Component.Button -> Parent-Component.Child-Ref: "1. onClick 触发调用"
Parent-Component.Child-Ref -> FancyInput-Component.useImperativeHandle: "2. 访问暴露的 'focus' 方法"
FancyInput-Component.useImperativeHandle -> FancyInput-Component.Internal-Input: "3. 操作内部 DOM 节点"

```

通过使用 `useRef` 和 `useImperativeHandle`，你可以管理那些超出 React 典型自顶向下数据流的交互，同时仍能保持组件 API 的清晰和可预测性。

---

讲解完 ref，你现在已经掌握了处理直接 DOM 操作和持久化可变状态的工具。接下来，让我们探讨如何优化应用程序的渲染性能。

下一节：[性能 Hook](./hooks-performance.md)
