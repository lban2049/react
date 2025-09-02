# 其他 Hook

除了管理状态、副作用和性能，React 还提供了几个专门的 Hook 来解决其他常见问题。本指南涵盖了一系列用于读取 context、生成稳定标识符、调试自定义 Hook 以及与外部数据源集成的 Hook。

<x-cards data-columns="2">
  <x-card data-title="useContext" data-icon="lucide:arrow-down-to-dot">
    无需逐层传递 prop 即可访问父组件中的数据。
  </x-card>
  <x-card data-title="useId" data-icon="lucide:fingerprint">
    为无障碍属性生成唯一的、稳定的 ID。
  </x-card>
  <x-card data-title="useDebugValue" data-icon="lucide:bug">
    在 React DevTools 中为你的自定义 Hook 显示自定义标签。
  </x-card>
  <x-card data-title="useSyncExternalStore" data-icon="lucide:link-2">
    以支持并发特性的方式订阅外部存储。
  </x-card>
</x-cards>

---

## useContext

`useContext` Hook 接受一个 context 对象（`React.createContext` 的返回值）并返回该 context 的当前值。这是在函数组件中使用 context 的主要方式，可以避免通过组件树的多个层级手动传递 props。

有关 context 概念的更多信息，请参阅 [Context](./core-apis-context.md) 指南。

**语法**

```javascript
const value = useContext(SomeContext);
```

**参数**

| 名称 | 类型 | 描述 |
|---|---|---|
| `Context` | `ReactContext` | 由 `React.createContext` 返回的 context 对象。 |

**返回值**

当前的 context 值，该值由组件树中上层最近的 `<MyContext.Provider>` 的 `value` prop 决定。

**示例**

此示例展示了 `ThemedButton` 组件如何在不接收 prop 的情况下访问当前主题。

```javascript
import React, { createContext, useContext } from 'react';

// 1. 创建一个 context
const ThemeContext = createContext('light');

// 2. 一个使用 context 的组件
function ThemedButton() {
  const theme = useContext(ThemeContext);
  return <button className={`theme-${theme}`}>A {theme} button</button>;
}

// 3. 一个提供 context 的组件
export default function App() {
  return (
    <ThemeContext.Provider value="dark">
      <ThemedButton />
    </ThemeContext.Provider>
  );
}
```

---

## useId

`useId` 是一个用于生成在服务器和客户端渲染中都保持稳定的唯一 ID 的 Hook。这对于避免 hydration 不匹配问题很有用，特别是对于像 `htmlFor` 和 `id` 这样的无障碍属性。

**语法**

```javascript
const id = useId();
```

**参数**

无。

**返回值**

一个唯一的字符串 ID。对于给定的组件，该 ID 在多次渲染中保持稳定。

**示例**

在这里，`useId` 生成一个匹配的 ID 来连接标签和输入字段，这对于屏幕阅读器至关重要。

```javascript
import React, { useId } from 'react';

function EmailField() {
  const id = useId();
  return (
    <div>
      <label htmlFor={id}>Email Address</label>
      <input id={id} type="email" name="email" />
    </div>
  );
}
```

---

## useDebugValue

`useDebugValue` 是一个允许你在 React DevTools 中为你的自定义 Hook 显示自定义标签的 Hook。这使得检查和调试自定义 Hook 的内部状态变得更加容易。

**语法**

```javascript
useDebugValue(value, format?);
```

**参数**

| 名称 | 类型 | 描述 |
|---|---|---|
| `value` | `any` | 要在 React DevTools 中显示的值。 |
| `formatterFn`| `function` | 可选。一个用于格式化显示值的函数。它接收值作为参数，并应返回一个格式化后的值。这对于避免昂贵的格式化操作很有用，除非 Hook 真的被检查。 |

**示例**

在这个自定义 Hook 中，`useDebugValue` 在 DevTools 的组件检查器中提供了一个人类可读的状态。

```javascript
import { useState, useDebugValue } from 'react';

function useOnlineStatus() {
  const [isOnline, setIsOnline] = useState(true);

  // ... 检查在线状态的逻辑将放在这里 ...

  // 在 React DevTools 中此 Hook 旁边显示一个可读的标签
  useDebugValue(isOnline ? 'Online' : 'Offline');

  return isOnline;
}

export default function App() {
  const isOnline = useOnlineStatus();
  return <h1>{isOnline ? '✅ Online' : '❌ Disconnected'}</h1>;
}
```

当你在 React DevTools 中检查 `App` 组件时，你会看到 `OnlineStatus` Hook 的标签显示为“Online”或“Offline”。

---

## useSyncExternalStore

`useSyncExternalStore` 是一个用于读取和订阅外部数据源的 Hook，它与 React 的并发渲染特性兼容。它能确保在外部数据发生变化时组件会重新渲染，并防止在并发更新期间出现视觉撕裂。

它通常由与 React 集成的库使用，而不是直接在应用程序代码中使用。

**语法**

```javascript
const snapshot = useSyncExternalStore(subscribe, getSnapshot, getServerSnapshot?);
```

**参数**

| 名称 | 类型 | 描述 |
|---|---|---|
| `subscribe` | `function` | 一个将回调函数订阅到 store 的函数。它必须返回一个取消订阅的函数。 |
| `getSnapshot`| `function` | 一个返回 store 中当前数据快照的函数。 |
| `getServerSnapshot` | `function` | 可选。一个为服务器端渲染返回数据快照的函数。 |

**示例**

此示例演示了如何订阅浏览器的 `window.innerWidth` 属性。

```javascript
import { useSyncExternalStore } from 'react';

function subscribe(callback) {
  window.addEventListener('resize', callback);
  return () => {
    window.removeEventListener('resize', callback);
  };
}

function getSnapshot() {
  return window.innerWidth;
}

export default function App() {
  const width = useSyncExternalStore(subscribe, getSnapshot);

  return <h1>Window width: {width}</h1>;
}
```
现在，每当浏览器窗口大小调整时，此组件都将正确地重新渲染。

---

### 后续步骤

你现在已经探索了处理 context、唯一 ID、调试和外部状态的各种专用 Hook。要更深入地了解更复杂的 React 特性和模式，请继续阅读我们的[高级指南](./advanced.md)。