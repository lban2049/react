# 缓存

React 提供了一个强大的内置缓存机制，专为在单次服务器渲染过程中实现数据获取和计算的记忆化而设计。这使你能够从多个组件中获取数据，而无需对相同信息发起冗余请求，从而简化服务器端逻辑并提升性能。

关键是要理解 React 的缓存功能在不同环境下的行为有所不同。在服务器上，它提供的是单次请求缓存。在客户端，`cache` 函数目前没有缓存行为，其作用等同于一个恒等函数。

## `cache` 函数

React 中用于缓存的主要 API 是 `cache` 函数。你可以用 `cache` 包装一个函数（例如，数据获取函数）来对其结果进行记忆化。

当你使用一组参数调用一个被缓存的函数时，React 会存储其结果。如果在同一次渲染过程中，你再次使用相同的参数调用该函数，React 将返回已存储的结果，而不会重新执行该函数。

### 服务器端用法

在服务器上，`cache` 对于数据请求的去重非常有效。缓存会为每个服务器请求创建，并在请求完成后被丢弃。

```javascript Server-Side Data Fetching icon=logos:react
import { cache } from 'react';

export const getUser = cache(async (userId) => {
  const response = await fetch(`https://api.example.com/users/${userId}`);
  if (!response.ok) {
    throw new Error('Failed to fetch user');
  }
  return response.json();
});

// --- 在 ComponentA.js 中 ---
// const user = await getUser(123); // 发起网络请求

// --- 在 ComponentB.js 中（同一次渲染期间） ---
// const user = await getUser(123); // 返回缓存数据，不发起新的网络请求
```

### 客户端行为

在客户端环境（即非 React 服务器组件环境）中，`cache` 不执行任何缓存操作。它只是简单地返回原始函数。在客户端暴露此 API 是为了让那些在服务器上使用 `cache` 的共享组件也能在客户端上运行而不会出错。开发者必须意识到这种行为上的差异。

```javascript Client-Side Behavior icon=logos:react
// 在客户端环境中，此代码...
import { cache } from 'react';

const calculate = (a, b) => a + b;
const cachedCalculate = cache(calculate);

// ...的行为与此完全相同：

const calculate = (a, b) => a + b;
const cachedCalculate = calculate;
```

### 缓存的工作原理

缓存机制会根据传递给函数的参数生成一个键。

- **原始类型参数**（字符串、数字、布尔值等）存储在 `Map` 中。
- **对象和函数参数**存储在 `WeakMap` 中。使用 `WeakMap` 可以确保缓存不会阻止对其他地方不再引用的对象进行垃圾回收，从而防止内存泄漏。

如果一个被缓存的函数抛出错误，React 会缓存该错误。后续使用相同参数的调用将重新抛出这个被缓存的错误，而不会重新执行该函数。

## `cacheSignal` 函数

在执行数据获取时，处理请求取消是一个好习惯。`cacheSignal` 函数提供一个与服务器请求生命周期绑定的 `AbortSignal`。

如果其所属的渲染被取消，该信号就会被中止。你可以将此信号传递给 `fetch` 以自动取消网络请求。

```javascript Using cacheSignal with fetch icon=logos:react
import { cache, cacheSignal } from 'react';

const fetchPost = cache(async (postId) => {
  const signal = cacheSignal();
  const response = await fetch(`https://api.example.com/posts/${postId}`, {
    signal, // 将 signal 传递给 fetch
  });
  return response.json();
});
```

与 `cache` 类似，`cacheSignal` 也主要用于服务器端。在客户端，它会返回 `null`。

---

通过理解 React 的缓存 API，你可以构建更高效的服务器渲染应用程序。关键在于记住它在服务器上的单次请求特性以及在客户端上的直通行为。要更深入地了解环境差异，请参阅我们的指南 [服务器 vs. 客户端环境](./advanced-server-vs-client.md)。