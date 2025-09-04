# 其他 Hook

除了管理状态、副作用和性能之外，React 还提供了其他几个专门的 Hook 来满足组件内的不同需求。本节将介绍用于消费上下文、生成稳定 ID、订阅外部存储以及辅助调试的 Hook。

这些 Hook 为 prop 逐层传递、无障碍性、与非 React 系统集成以及改善开发体验等常见挑战提供了解决方案。

有关上下文概念的介绍，请参见 [Context](./core-apis-context.md) 文档。

---

## `useContext`

`useContext` Hook 允许组件订阅 React 上下文，而无需引入嵌套。它提供了一种更简洁的方式来消费组件树中上层 `Context.Provider` 提供的值。

### 语法

```javascript
const value = useContext(ContextObject);
```

### 参数

| Parameter | Type | Description |
|---|---|---|
| `ContextObject` | `ReactContext` | 由 `React.createContext` 返回的上下文对象。 |

### 返回值

组件的当前上下文值，由组件树中其上方最近的 `Context.Provider` 的 `value` prop 决定。

### 示例

此示例演示了深度嵌套的 `ThemedButton` 组件如何在没有通过 props 逐层传递的情况下访问 `theme` 值。

```javascript
import React, { createContext, useContext } from 'react';

// 1. 创建一个 context
const ThemeContext = createContext('light');

// 一个使用该 hook 的组件
function ThemedButton() {
  // 3. 消费 context 的值
  const theme = useContext(ThemeContext);
  return <button style={{ background: theme === 'dark' ? '#333' : '#FFF', color: theme === 'dark' ? '#FFF' : '#333' }}>I am a {theme} button</button>;
}

// 一个无需了解 theme 的中间组件
function Toolbar() {
  return (
    <div>
      <ThemedButton />
    </div>
  );
}

export default function App() {
  // 2. 提供 context 的值
  return (
    <ThemeContext.Provider value="dark">
      <Toolbar />
    </ThemeContext.Provider>
  );
}
```

---

## `useId`

`useId` 是一个用于生成在服务端和客户端渲染之间保持稳定的唯一 ID 的 Hook。这主要用于为 `htmlFor` 和 `id` 等无障碍属性生成 ID 时，避免 hydration 不匹配的问题。

### 语法

```javascript
const uniqueId = useId();
```

### 参数

无。

### 返回值

一个唯一的、稳定的字符串 ID。该 ID 在服务端渲染和客户端渲染的输出之间保证相同。

### 示例

在这里，`useId` 生成一个一致的 ID 来关联 `label` 和 `input` 元素，这对于无障碍性至关重要。

```javascript
import React, { useId } from 'react';

function EmailField() {
  const id = useId();
  return (
    <>
      <label htmlFor={id}>Email address</label>
      <input id={id} type="email" name="email" />
    </>
  );
}

export default function SignupForm() {
  return (
    <form>
      <EmailField />
      {/* 你可以渲染多个实例，每个实例都会有唯一的 ID */}
      <EmailField />
    </form>
  );
}
```

---

## `useSyncExternalStore`

此 Hook 旨在让 React 组件能够安全高效地订阅外部数据源或存储。它能确保在外部数据发生变化时组件正确地重新渲染，并与并发渲染功能兼容，从而防止视觉撕裂 (visual tearing)。

它通常被状态管理库使用，或在与浏览器 API 集成时使用。

### 语法

```javascript
const snapshot = useSyncExternalStore(subscribe, getSnapshot, getServerSnapshot?);
```

### 参数

| Parameter | Type | Description |
|---|---|---|
| `subscribe` | `(() => void) => () => void` | 一个接收 `callback` 函数并将其订阅到 store 的函数。它必须返回一个用于处理清理/取消订阅的函数。 |
| `getSnapshot` | `() => T` | 一个返回 store 中当前数据快照的函数。如果返回值发生变化，组件将重新渲染。 |
| `getServerSnapshot` | `() => T` | （可选）一个为服务端渲染 (SSR) 返回数据初始快照的函数。 |

### 返回值

来自外部 store 的当前数据快照。

### 示例

此示例展示了如何使用 `useSyncExternalStore` 订阅浏览器的在线状态。

```javascript
import { useSyncExternalStore } from 'react';

function subscribe(callback) {
  window.addEventListener('online', callback);
  window.addEventListener('offline', callback);
  return () => {
    window.removeEventListener('online', callback);
    window.removeEventListener('offline',callback);
  };
}

function getSnapshot() {
  return navigator.onLine;
}

// 服务端快照总是假定用户初始状态为在线。
function getServerSnapshot() {
  return true;
}

export default function OnlineStatus() {
  const isOnline = useSyncExternalStore(subscribe, getSnapshot, getServerSnapshot);

  return <h1>{isOnline ? '✅ Online' : '❌ Disconnected'}</h1>;
}
```

---

## `useDebugValue`

`useDebugValue` 是一个可以在 React DevTools 中为你的自定义 Hook 显示自定义标签的 Hook。它仅用于调试，在生产构建中无效。

### 语法

```javascript
useDebugValue(value, formatFn?);
```

### 参数

| Parameter | Type | Description |
|---|---|---|
| `value` | `any` | 在 React DevTools 中显示在自定义 Hook 名称旁边的值。 |
| `formatFn` | `(value) => formattedValue` | （可选）一个用于格式化显示值的函数。该函数仅在 DevTools 中检查组件时被调用，从而避免在其他情况下执行可能开销很大的格式化操作。 |

### 示例

在此示例中，我们创建了一个自定义 `useOnlineStatus` Hook，并使用 `useDebugValue` 在 DevTools 中显示一个用户友好的状态字符串。

```javascript
import React, { useState, useEffect, useDebugValue } from 'react';

// 自定义 Hook
function useOnlineStatus() {
  const [isOnline, setIsOnline] = useState(true);

  useEffect(() => {
    function handleOnline() { setIsOnline(true); }
    function handleOffline() { setIsOnline(false); }
    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);
    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, []);

  // 在 React DevTools 中显示一个自定义标签
  useDebugValue(isOnline ? 'Online' : 'Offline');

  return isOnline;
}

export default function App() {
  const isOnline = useOnlineStatus();
  return <p>User is: {isOnline ? 'Online' : 'Offline'}</p>;
}

```
当你在 React DevTools 中检查 `App` 组件时，你将在 Hooks 树中看到 `OnlineStatus: "Online"`，这使你能够轻松地一目了然地查看自定义 Hook 的当前状态。

---

本节介绍了一系列处理从上下文到调试等特定问题的 Hook。在对所有内置 Hook 有了深入的理解后，你就能够构建复杂且高效的 React 应用程序。

要探索更复杂的 React 功能、模式和环境，请继续阅读[高级指南](./advanced.md)。