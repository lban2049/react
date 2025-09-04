# 缓存

React 提供了一个内置的缓存机制，主要用于在单次服务器渲染过程中对数据获取和计算进行记忆化处理。这有助于避免重复工作，并在树中的多个组件访问相同数据时确保数据一致性。这在 React Server Components 的情境下尤其有用。

## `React.cache`

该功能的主要 API 是 `React.cache`。它是一个高阶函数，用于包装另一个函数，并根据调用时传入的参数对其结果进行记忆化。

### 基本用法

如需使用，请从 React 中导入 `cache` 并包装一个函数，例如数据获取函数。

```javascript
// utils/data.js
import { cache } from 'react';

export const getUser = cache(async (id) => {
  const res = await fetch(`https://api.example.com/users/${id}`);
  return res.json();
});
```

现在，你可以在组件中正常调用 `getUser`。在服务器的单次渲染过程中，如果多个组件调用 `getUser(123)`，底层的 `async` 函数将只执行一次。后续使用相同 `id` 的调用将接收缓存的结果。

```javascript
// components/UserProfile.js
import { getUser } from '../utils/data';

async function UserProfile({ id }) {
  const user = await getUser(id);
  return <h1>{user.name}</h1>;
}
```

### 记忆化工作原理

`cache` 函数会根据传递给被包装函数的参数创建一个键。

- **基本类型**：对于字符串、数字和布尔值等基本类型值，它使用值相等性进行判断。
- **对象和函数**：对于对象和函数，它使用引用相等性进行判断。这意味着使用不同的对象实例（即使它们内容相同）进行两次独立的调用，将会导致缓存未命中。

```javascript
// 缓存未命中，因为每次调用都创建了一个新对象
const user1 = await fetchUser({ id: 1 });
const user2 = await fetchUser({ id: 1 });

// 缓存命中，因为使用了相同的对象引用
const params = { id: 1 };
const user3 = await fetchUser(params);
const user4 = await fetchUser(params);
```

这种行为是通过对对象/函数参数使用 `WeakMap`、对基本类型参数使用 `Map` 来实现的，从而为每个唯一的参数序列创建一棵缓存节点树。

### 缓存流程图

下图展示了缓存函数调用的逻辑。

```d2
direction: down

Component: {
  shape: rectangle
}

Cache-Storage: {
  label: "React 的单次请求缓存"
  shape: cylinder
}

Data-Source: {
  label: "数据库 / API"
  shape: cylinder
}

cachedFunction: {
  label: "cachedFunction(args)"
  shape: diamond
}

Component -> cachedFunction: "1. 调用"

cachedFunction -> Cache-Storage: "2. 检查 'args' 键"

Cache-Storage -> cachedFunction: "3a. 缓存命中\n(返回缓存值)" {
  style.stroke: "#52c41a"
}

cachedFunction -> Data-Source: "3b. 缓存未命中\n(执行函数)" {
  style.stroke: "#faad14"
}

Data-Source -> cachedFunction: "4. 返回结果"

cachedFunction -> Cache-Storage: "5. 将结果存入 'args' 键"

cachedFunction -> Component: "6. 返回值"

```

## 特定环境下的行为

`cache` 的行为在服务器和客户端环境中有所不同。

- **服务器**：在 React Server Components 等环境中，`cache` 会执行单次请求范围内的记忆化。缓存会在请求开始时创建，并在渲染完成后被丢弃。
- **客户端**：在客户端，`cache` 目前不起任何作用（no-op）。它会返回原始函数，不添加任何缓存行为。提供此 API 是为了兼容性，允许使用 `cache` 的组件在服务器和客户端上都无需修改即可运行。未来版本可能会引入完整的客户端缓存实现。

## `cacheSignal`

为了处理请求取消，React 提供了 `cacheSignal` 函数。它会返回一个与当前请求的缓存作用域相关联的 `AbortSignal`。你可以将此信号传递给 `fetch` 等操作，以便在渲染被中止时自动取消这些操作。

```javascript
import { cache, cacheSignal } from 'react';

export const getPost = cache(async (id) => {
  const signal = cacheSignal();
  const res = await fetch(`https://api.example.com/posts/${id}`, { signal });
  return res.json();
});
```

与 `cache` 类似，`cacheSignal` 也主要用于服务器端。在客户端，它会返回 `null`。

## 缓存失效

虽然服务器缓存会在请求之间自动清除，但你可能需要在客户端手动使其失效，例如在数据变更后。`useCacheRefresh` Hook 正是为此目的而设。

调用 `useCacheRefresh` 返回的 `refresh` 函数会使缓存失效，从而导致被缓存的函数重新执行。

```javascript
import { useCacheRefresh } from 'react';

function RefreshButton() {
  const refresh = useCacheRefresh();

  function handleClick() {
    // 使缓存失效并触发重新渲染
    refresh();
  }

  return <button onClick={handleClick}>Refresh Data</button>;
}
```

---

理解 React 的缓存是构建高性能服务器渲染应用的关键。要深入了解不同渲染环境之间的差异，请参阅[服务器与客户端环境](./advanced-server-vs-client.md)指南。