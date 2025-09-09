# Ref Hooks

Ref Hooks 提供了一种直接访问 DOM 节点或保留一个在变更时不会触发重新渲染的可变值的方法。它们对于与第三方库集成、管理焦点或存储在组件整个生命周期内持续存在但又不属于渲染逻辑一部分的值至关重要。

本节涵盖了两个主要的 Ref Hooks：

<x-cards>
  <x-card data-title="useRef" data-icon="lucide:mouse-pointer-square">
    创建一个可变的 ref 对象，其 `.current` 属性可以持有一个值，通常是对 DOM 节点的引用。
  </x-card>
  <x-card data-title="useImperativeHandle" data-icon="lucide:hand-pointing">
    在使用 `ref` 和 `forwardRef` 时，自定义暴露给父组件的实例值。
  </x-card>
</x-cards>

---

## `useRef`

The `useRef` Hook 返回一个可变的 ref 对象。该对象只有一个属性，`current`，你可以将其设置为任何值。`useRef` 在两个主要场景中非常有用：访问 DOM 元素和存储不会引起重新渲染的可变值。

### 语法

```javascript useRef Hook 签名 icon=logos:javascript
function useRef<T>(initialValue: T): {current: T};
```

### 参数

| Parameter | Type | Description |
|---|---|---|
| `initialValue` | `T` | 你希望 ref 对象的 `current` 属性初始化的值。它可以是任何类型的值。 |

### 返回值

`useRef` 返回一个带有 `current` 属性的单一对象。最初，`current` 被设置为你提供的 `initialValue`。之后你可以将其设置为其他值。如果你将 ref 对象传递给 JSX 节点的 `ref` 属性，React 会将相应的 DOM 节点放入其 `current` 属性中。

### 示例 1：访问 DOM 元素

`useRef` 的一个常见用例是直接访问 DOM 元素，从而允许你调用其上的命令式方法，例如 `focus()`。

```javascript 使用 useRef 聚焦输入框 icon=logos:javascript
import React, { useRef } from 'react';

function FocusInput() {
  const inputRef = useRef(null);

  const handleFocusClick = () => {
    // 通过 inputRef.current 直接访问 DOM 节点
    if (inputRef.current) {
      inputRef.current.focus();
    }
  };

  return (
    <>
      <input ref={inputRef} type="text" placeholder="点击按钮进行聚焦" />
      <button onClick={handleFocusClick}>聚焦输入框</button>
    </>
  );
}

export default FocusInput;
```
在此示例中，`inputRef` 附加到 `<input>` 元素上。当点击按钮时，`inputRef.current` 持有实际的 DOM 节点，我们可以调用其 `focus()` 方法。

### 示例 2：存储可变值

你也可以使用 `useRef` 来持有任何可变值，类似于类中的实例变量。与 state 的关键区别在于，更新 ref 不会触发组件的重新渲染。

```javascript 存储 Interval ID icon=logos:javascript
import React, { useState, useRef, useEffect } from 'react';

function Timer() {
  const [count, setCount] = useState(0);
  const intervalRef = useRef(null);

  useEffect(() => {
    // 启动 interval
    intervalRef.current = setInterval(() => {
      setCount(prevCount => prevCount + 1);
    }, 1000);

    // 在组件卸载时清除 interval
    return () => {
      clearInterval(intervalRef.current);
    };
  }, []); // 空依赖数组意味着此 effect 仅在挂载时运行一次

  const handleStopTimer = () => {
    clearInterval(intervalRef.current);
  };

  return (
    <div>
      <p>计时器: {count} 秒</p>
      <button onClick={handleStopTimer}>停止计时器</button>
    </div>
  );
}

export default Timer;
```
在这里，`intervalRef` 存储了 `setInterval` 返回的 ID。我们可以在稍后的 `handleStopTimer` 函数或 `useEffect` 清理函数中访问此 ID 以清除 interval，而不会在每次设置 ID 时导致组件重新渲染。

---

## `useImperativeHandle`

`useImperativeHandle` 允许你自定义子组件暴露的 ref 句柄。你可以定义一组特定的命令式函数供父组件调用，而不是暴露整个 DOM 节点。此 hook 应与 `forwardRef` 一起使用。

### 语法

```javascript useImperativeHandle Hook 签名 icon=logos:javascript
function useImperativeHandle<T>(
  ref: {current: T | null} | ((inst: T | null) => mixed) | null | void,
  create: () => T,
  deps: Array<mixed> | void | null,
): void;
```

### 参数

| Parameter | Type | Description |
|---|---|---|
| `ref` | `RefObject` | 通过 `forwardRef` 从父组件传递下来的 `ref`。 |
| `create` | `() => T` | 一个返回自定义句柄的函数。此句柄可以是一个包含方法和属性的对象。 |
| `deps` | `Array<any>` | （可选）一个依赖数组。每当此数组中的值发生变化时，`create` 函数会重新执行。 |

### 示例：暴露自定义 API

此示例展示了一个自定义输入组件，它仅向其父组件暴露 `focus` 和 `clear` 方法，隐藏了底层的 `input` 元素实现。

```javascript CustomInput 组件 icon=logos:javascript
import React, { useRef, useImperativeHandle, forwardRef } from 'react';

const CustomInput = forwardRef((props, ref) => {
  const internalInputRef = useRef(null);

  // 向父组件暴露一个自定义句柄
  useImperativeHandle(ref, () => ({
    focus: () => {
      internalInputRef.current.focus();
    },
    clear: () => {
      internalInputRef.current.value = '';
    }
  }));

  return <input ref={internalInputRef} {...props} />;
});

// 父组件
function App() {
  const customInputRef = useRef(null);

  const handleFocus = () => {
    customInputRef.current.focus();
  };

  const handleClear = () => {
    customInputRef.current.clear();
  };

  return (
    <div>
      <CustomInput ref={customInputRef} placeholder="我有一个自定义 API" />
      <button onClick={handleFocus}>聚焦输入框</button>
      <button onClick={handleClear}>清空输入框</button>
    </div>
  );
}

export default App;
```
在 `App` 组件中，`customInputRef.current` 并不指向 DOM `<input>` 元素。相反，它指向在 `useImperativeHandle` 中定义的对象 `{ focus: () => {..}, clear: () => {..} }`。

## 总结

Ref Hooks 是一个强大的工具，可以在必要时跳出标准的声明式渲染流程。

- 使用 `useRef` 访问 DOM 元素或保留不影响渲染的可变数据。
- 将 `useImperativeHandle`与 `forwardRef` 结合使用，为你的组件创建一个简洁且受限的命令式 API，向父组件隐藏实现细节。

现在你已经了解了如何管理 ref，可以探索优化应用程序性能的方法。在 [Performance Hooks](./hooks-performance.md) 部分了解更多信息。