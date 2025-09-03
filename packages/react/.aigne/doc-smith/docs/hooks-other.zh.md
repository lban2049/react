# 其他 Hooks

本节介绍了一组专门的 Hooks，用于处理各种任务，包括读取上下文、生成唯一 ID、与外部数据源集成以及调试自定义 Hooks。虽然 [State Hooks](./hooks-state.md) 负责管理组件数据，[Effect Hooks](./hooks-effect.md) 负责处理副作用，但本节介绍的 Hooks 为 React 应用程序中的其他常见需求提供了解决方案。

---

## `useContext`

接受一个上下文对象（`React.createContext` 的返回值）并返回当前的上下文值。当前上下文值由组件树中，离调用组件最近的 `<MyContext.Provider>` 的 `value` prop 决定。

当组件上方最近的 `<MyContext.Provider>` 更新时，此 Hook 会使用最新的上下文 `value` 来触发重新渲染。

### API

```javascript
const value = useContext(SomeContext);
```

### 参数

| 参数 | 类型 | 描述 |
|---|---|---|
| `Context` | React Context 对象 | 从 `React.createContext` 返回的上下文对象。 |

### 返回值

返回调用组件的上下文值。

### 示例

```javascript
import React, { createContext, useContext } from 'react';

// 1. 创建一个 context
const ThemeContext = createContext('light');

// 2. 一个使用 context 的组件
function ThemedButton() {
  const theme = useContext(ThemeContext);
  return <button>Current theme is: {theme}</button>;
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

## `useId`

`useId` 是一个用于生成在服务端和客户端之间保持稳定的唯一 ID 的 Hook，这对于防止在服务端渲染应用中出现 hydration 不匹配至关重要。它主要用于需要关联两个元素（例如 `<label>` 和 `<input>`）的可访问性属性。

### API

```javascript
const uniqueId = useId();
```

### 参数

此 Hook 没有参数。

### 返回值

一个唯一且稳定的字符串 ID。该 ID 以 `:r` 为前缀，以确保它不会与 CSS 选择器冲突。

### 示例

```javascript
import { useId } from 'react';

function FormField() {
  const id = useId();
  return (
    <div>
      <label htmlFor={id}>Your Email:</label>
      <input id={id} type="email" name="email" />
    </div>
  );
}

export default function SignupForm() {
  return (
    <form>
      <p>Sign up for our newsletter:</p>
      <FormField />
      <FormField />
    </form>
  );
}
```
在上面的示例中，每个 `FormField` 实例都会获得自己的唯一 ID，从而将每个 label 与其 input 正确关联。

---

## `useDebugValue`

`useDebugValue` 可用于在 React DevTools 中为自定义 Hooks 显示标签。这有助于检查和调试自定义 Hooks 的内部状态。

**注意：**此 Hook 仅在开发模式下生效，在生产构建中会被忽略。

### API

```javascript
useDebugValue(value, formatFn?);
```

### 参数

| 参数 | 类型 | 描述 |
|---|---|---|
| `value` | `any` | 要在 React DevTools 中自定义 Hook 旁边显示的值。 |
| `formatFn` | `function` | （可选）一个格式化函数，仅在 DevTools 打开时调用。它接收 `value` 并应返回一个格式化后的显示值。这样可以推迟可能开销很大的格式化操作。 |

### 返回值

此 Hook 不返回任何内容（`void`）。

### 示例

```javascript
import { useState, useDebugValue } from 'react';

function useOnlineStatus() {
  const [isOnline, setIsOnline] = useState(navigator.onLine);

  // ... 监听在线/离线事件的逻辑 ...

  // 此标签将显示在 React DevTools 中
  useDebugValue(isOnline ? 'Online' : 'Offline');

  return isOnline;
}

export default function ChatApp() {
  const isOnline = useOnlineStatus();
  return <h1>{isOnline ? '✅ Connected' : '❌ Disconnected'}</h1>;
}
```
当你在 React DevTools 中检查 `ChatApp` 组件时，你将看到一个 `OnlineStatus` Hook，旁边带有“Online”或“Offline”的标签。

---

## `useSyncExternalStore`

`useSyncExternalStore` 是一个用于订阅外部数据源的 Hook。它旨在与并发渲染功能兼容，并确保在外部数据变化时组件能正确地重新渲染，从而避免出现视觉撕裂。

这对于与非 React 状态构建的第三方状态管理库或浏览器 API 进行集成非常有用。

### API

```javascript
const state = useSyncExternalStore(subscribe, getSnapshot, getServerSnapshot?);
```

### 参数

| 参数 | 类型 | 描述 |
|---|---|---|
| `subscribe` | `function` | 一个将回调函数订阅到 store 的函数。它必须返回一个 `unsubscribe` 函数。 |
| `getSnapshot` | `function` | 一个返回 store 中当前数据值（快照）的函数。 |
| `getServerSnapshot`| `function` | （可选）一个为服务端渲染 (SSR) 返回数据初始快照的函数。 |

### 返回值

来自外部 store 的当前值。

### 示例

此示例订阅浏览器的 `window.innerWidth` 以显示当前窗口宽度。

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
  return <p>Window width: {width}px</p>;
}
```

---

这些实用工具 Hooks 解决了 React 生态系统中的一系列特定需求。如果遇到更复杂的挑战，可以探索[高级指南](./advanced.md)或查阅完整的[API 参考](./api-reference.md)。