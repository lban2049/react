# 缓存

React 提供了内置的缓存功能，可以记忆化数据获取或计算的结果。这在服务器环境中（例如使用 React Server Components 时）尤其强大，你可以在多个组件中获取数据，而不会在单次渲染过程中对同一数据发起冗余请求。

本指南将介绍用于记忆化的 `cache` 函数、其在服务器和客户端上的行为差异，以及用于管理缓存操作的相关 API。

## `cache` 函数

用于缓存的主要 API 是 `cache` 函数。它包装一个函数，并记忆化其返回值。当你在同一次服务器渲染中用相同的参数多次调用被包装的函数时，它将只执行一次原始函数。后续的调用将接收到缓存的结果。

这对于可能在整个组件树的各个组件中被调用的数据获取函数尤其有用。

**使用示例**

```javascript
import { cache } from 'react';

// 定义一个数据获取函数
const fetchUserDetails = async (userId) => {
  const response = await fetch(`https://api.example.com/users/${userId}`);
  if (!response.ok) {
    throw new Error('Failed to fetch user');
  }
  return response.json();
};

// 使用 cache 包装它
export const getUser = cache(fetchUserDetails);
```

现在，任何服务器组件都可以调用 `getUser(id)`，而无需担心在单个请求-响应生命周期内对同一用户发起重复的网络请求。

```jsx
// ComponentA.js
import { getUser } from './data';

async function ComponentA({ id }) {
  const user = await getUser(id); // 获取并缓存用户
  return <div>{user.name}</div>;
}

// ComponentB.js
import { getUser } from './data';

async function ComponentB({ id }) {
  const user = await getUser(id); // 返回缓存的用户数据
  return <p>Email: {user.email}</p>;
}
```

### 工作原理

The `cache` 函数的工作原理是根据传递给被包装函数的参数创建一个嵌套的 map 结构。缓存的作用域限定在每个服务器请求内，确保一个用户的请求数据不会泄露给另一个用户。

- **原始类型参数**：字符串、数字和布尔值等值类型在 `Map` 中以其值作为键。
- **对象参数**：对象、函数和 symbol 在 `WeakMap` 中以其引用作为键。

如果被包装的函数抛出错误，该错误也会被缓存。之后任何使用相同参数的调用都将重新抛出缓存的错误。

其底层机制依赖一个请求特定的调度器来存储缓存数据。

```d2
direction: down

"请求到达": {
  shape: step
  "React 渲染过程": {
    "getUser(123)": {
      "cache(fetchUserDetails)": {
        label: "fn + args 是否有缓存结果？"
        shape: diamond
      }
    }
  }
}

"请求到达" -> "React 渲染过程.getUser(123).cache(fetchUserDetails)"

"React 渲染过程.getUser(123).cache(fetchUserDetails)" -> "执行 fetchUserDetails(123)": {
  label: 否
  style.stroke: red
}

"执行 fetchUserDetails(123)" -> "将结果存储在请求缓存中": {
  shape: cylinder
}

"将结果存储在请求缓存中" -> "返回结果"

"React 渲染过程.getUser(123).cache(fetchUserDetails)" -> "返回缓存结果": {
  label: 是
  style.stroke: green
}

"返回缓存结果" -> "返回结果": {
  shape: step
}
```

### 环境差异：服务器 vs. 客户端

`cache` 的行为因环境而异，它主要为服务器设计。

| 环境 | `cache` 行为 |
|---|---|
| **服务器** | 在单个服务器请求期间记忆化函数调用。这是完整的、预期的实现。 |
| **客户端** | 相当于一个空操作（no-op）。每次调用都会执行被包装的函数，不进行缓存。这是一种临时行为，未来版本中可能会实现客户端缓存。 |

对于在服务器和客户端两种环境中运行的共享组件来说，这一区别非常重要。在编写这些组件时，必须考虑到客户端不支持缓存这一情况。

## 使用 `cacheSignal` 中断请求

对于 `fetch` 等异步操作，处理请求取消是一个很好的实践。`cacheSignal` 函数提供一个请求作用域的 `AbortSignal`，如果服务器端渲染被取消，该信号将会被中止。

```javascript
import { cache, cacheSignal } from 'react';

export const getUser = cache(async (id) => {
  const signal = cacheSignal();
  const response = await fetch(`https://api.example.com/users/${id}`, {
    signal, // 将 signal 传递给 fetch
  });
  return response.json();
});
```

与 `cache` 类似，`cacheSignal` 也依赖于环境：
- **在服务器上**，它返回一个与请求绑定的 `AbortSignal`。
- **在客户端上**，它返回 `null`。

## 不稳定功能：刷新缓存

React 暴露了一个不稳定的 Hook `useCacheRefresh`，用于以编程方式使当前请求的整个缓存失效并触发重新渲染。

> **注意：** 此 API 尚不稳定，应谨慎使用。其行为和签名可能会在未来版本中发生变化。

```jsx
import { unstable_useCacheRefresh as useCacheRefresh } from 'react';

function UserProfile({ id }) {
  const refresh = useCacheRefresh();
  const user = getUser(id);

  return (
    <div>
      <h1>{user.name}</h1>
      <button onClick={() => refresh()}>Refresh</button>
    </div>
  );
}
```

调用 `refresh()` 将清除 `cache` 中的记忆化结果，并重新运行服务器组件。

---

缓存是优化服务器渲染应用程序的强大模式。要更好地理解其最有效的应用场景，请参阅我们的指南：[服务器 vs. 客户端环境](./advanced-server-vs-client.md)。