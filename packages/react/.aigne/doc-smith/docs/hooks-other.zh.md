# 其他 Hook

除了管理 state、effect 和 ref，React 还提供了一组专门的 Hook 来解决其他常见问题。这些 Hook 用于处理诸如无需通过 props 逐层传递即可访问共享数据、为可访问性生成唯一 ID、与外部数据源集成以及改进自定义 Hook 的调试体验等任务。本节将探讨这些强大的实用工具。

<x-cards>
  <x-card data-title="useContext" data-icon="lucide:merge">
    无需在每一层手动向下传递 props，即可从父级 context provider 访问数据。
  </x-card>
  <x-card data-title="useId" data-icon="lucide:fingerprint">
    生成唯一的、稳定的 ID，这些 ID 对于服务器渲染和客户端 hydration 是安全的。
  </x-card>
  <x-card data-title="useDebugValue" data-icon="lucide:bug">
    在 React DevTools 中为你的自定义 Hook 显示自定义的、有用的标签。
  </x-card>
  <x-card data-title="useSyncExternalStore" data-icon="lucide:database-zap">
    以与并发渲染兼容的方式订阅外部数据源和 store。
  </x-card>
</x-cards>

---

## useContext

`useContext` Hook 提供了一种从 `React.Context` 消费值的方法。它接受一个 context 对象（`React.createContext` 返回的值），并返回该 context 的当前值。这使你可以将数据深入传递到组件树中，而无需手动逐层向下传递 props。

有关创建和提供 context 的更多详细信息，请参阅 [Context](./core-apis-context.md) 文档。

**签名**
```javascript
const value = useContext(MyContext);
```

**参数**

| Parameter | Type | Description |
|---|---|---|
| `MyContext` | React Context 对象 | 由 `React.createContext` 返回的、你希望订阅的 context 对象。 |

**示例**

```javascript ThemeContext Example icon=logos:react
import React, { createContext, useContext, useState } from 'react';

// 1. 创建一个 context
const ThemeContext = createContext('light');

// 2. 一个消费 context 的组件
function ThemedButton() {
  const theme = useContext(ThemeContext);
  const style = {
    background: theme === 'dark' ? '#282c34' : '#ffffff',
    color: theme === 'dark' ? '#ffffff' : '#282c34',
    border: '1px solid #ccc',
    padding: '8px 16px',
    cursor: 'pointer'
  };
  return <button style={style}>当前主题：{theme}</button>;
}

// 3. 提供 context 的父组件
export default function App() {
  const [theme, setTheme] = useState('light');

  const toggleTheme = () => {
    setTheme(current => (current === 'light' ? 'dark' : 'light'));
  };

  return (
    <ThemeContext.Provider value={theme}>
      <button onClick={toggleTheme}>切换主题</button>
      <hr style={{margin: '1em 0'}} />
      <ThemedButton />
    </ThemeContext.Provider>
  );
}
```
在此示例中，无论 `App` 和 `ThemedButton` 之间有多少层组件，`ThemedButton` 都使用 `useContext(ThemeContext)` 直接从 `App` 中的 `ThemeContext.Provider` 获取当前主题值。

---

## useId

`useId` 是一个用于生成在服务器和客户端都保持稳定的唯一 ID 的 Hook。这对于避免服务器渲染应用中的 hydration 不匹配问题至关重要。其主要用例是为可访问性连接相关元素，例如将 `<label>` 链接到 `<input>`。

**签名**
```javascript
const uniqueId = useId();
```

**示例**

```javascript Accessible Form Field icon=logos:react
import React, { useId } from 'react';

function EmailField() {
  const id = useId();
  return (
    <div>
      <label htmlFor={id}>邮箱：</label>
      <input id={id} type="email" name="email" />
    </div>
  );
}

export default function NewsletterForm() {
  return (
    <form>
      <h3>注册我们的时事通讯</h3>
      <EmailField />
      <EmailField />
    </form>
  );
}
```
每个 `EmailField` 实例都将生成一个唯一的、稳定的 ID。这确保了 label 的 `htmlFor` 属性与其 input 字段的 `id` 正确对应，从而在不产生冲突的情况下保持可访问性。

---

## useDebugValue

`useDebugValue` 是一个仅限开发人员使用的 Hook，可让你在 React DevTools 中为自己的自定义 Hook 显示自定义标签。这可以通过一目了然地提供更有意义的信息，使调试复杂的自定义 Hook 变得更加容易。

此 Hook 在生产构建中无效。

**签名**
```javascript
useDebugValue(value, formatFn?);
```

**参数**

| Parameter | Type | Description |
|---|---|---|
| `value` | `any` | 要在 React DevTools 中显示的值。 |
| `formatFn` | `(value) => formattedValue` | 可选。一个用于格式化显示值的函数。它仅在检查组件时被调用，这可以为复杂的格式化操作优化性能。 |

**示例**

```javascript Custom Hook with Debug Value icon=logos:react
import { useState, useDebugValue, useEffect } from 'react';

function useFriendStatus(friendID) {
  const [isOnline, setIsOnline] = useState(null);

  useEffect(() => {
    // 在实际应用中，你会订阅一个状态服务
    const status = friendID % 2 === 0; // 模拟状态
    setIsOnline(status);
  }, [friendID]);

  // 在 DevTools 中显示一个有用的标签
  useDebugValue(isOnline ? 'Online' : 'Offline');

  return isOnline;
}

export default function FriendListItem({ friend }) {
  const isOnline = useFriendStatus(friend.id);
  return (
    <li style={{ color: isOnline ? 'green' : 'black' }}>
      {friend.name}
    </li>
  );
}
```
当你在 React DevTools 中检查 `FriendListItem` 组件时，你会看到 `FriendStatus` Hook 及其状态被标记为“Online”或“Offline”，从而可以轻松查看该 Hook 的状态。

---

## useSyncExternalStore

`useSyncExternalStore` 是一个专为订阅外部数据源而设计的 Hook。它能确保你的组件与 React 外部管理的数据（如第三方状态管理库、浏览器 API 或 WebSocket 连接）保持同步，并且这种同步方式与并发渲染功能兼容。这有助于防止 UI tearing（UI 撕裂）——即在渲染过程中 UI 显示不一致的状态。

**签名**
```javascript
const state = useSyncExternalStore(subscribe, getSnapshot, getServerSnapshot?);
```

**参数**

| Parameter | Type | Description |
|---|---|---|
| `subscribe` | `(callback) => unsubscribe` | 一个将 `callback` 订阅到外部 store 的函数。它必须返回一个处理取消订阅的清理函数。 |
| `getSnapshot` | `() => snapshot` | 一个返回 store 中数据当前值（快照）的函数。返回的值应该是不可变的。 |
| `getServerSnapshot` | `() => snapshot` | 可选。一个为服务器端渲染 (SSR) 和 hydration 返回初始数据快照的函数。 |

**示例：订阅浏览器 API**

```javascript Subscribing to Network Status icon=logos:react
import { useSyncExternalStore } from 'react';

function subscribe(callback) {
  window.addEventListener('online', callback);
  window.addEventListener('offline', callback);
  return () => {
    window.removeEventListener('online', callback);
    window.removeEventListener('offline', callback);
  };
}

function getSnapshot() {
  return navigator.onLine;
}

export default function ChatIndicator() {
  const isOnline = useSyncExternalStore(subscribe, getSnapshot);
  return <h1>{isOnline ? '✅ 在线' : '❌ 连接已断开'}</h1>;
}
```
该组件安全地订阅了浏览器的网络状态。`useSyncExternalStore` 保证了当网络状态改变时，组件会正确地重新渲染，并且即使在并发模式下，UI 也能保持一致。

---

通过掌握这些 Hook，你可以编写出更高效、更易于访问和维护的 React 应用。对于更复杂的场景和模式，请继续阅读我们的[高级指南](./advanced.md)。