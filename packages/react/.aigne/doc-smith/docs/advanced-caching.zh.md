# 缓存

React 提供了内置的缓存功能，主要用于服务器组件中的数据获取和记忆化。这些工具有助于在单次渲染过程中防止冗余的数据请求，从而提高性能并确保整个组件树的数据一致性。本指南将介绍 `cache` 函数及用于管理请求生命周期的相关 API。

要更广泛地了解不同的渲染环境，请参阅[服务器与客户端环境](./advanced-server-vs-client.md)指南。

## `cache` 函数

The `cache` function is the primary API for memoizing the result of a function. When you wrap a function with `cache`, React stores the return value for a given set of arguments. If the same function is called with the same arguments later in the same server render pass, React will return the stored value instead of re-executing the function.

这对于在组件树中从多个组件调用的数据获取函数特别有用。

### 服务器端用法

在服务器上，`cache` 会按请求对函数调用进行记忆化。它会处理成功的结果和抛出的错误，确保失败的数据获取不会在单次渲染中重复执行并多次抛出相同的错误。

```javascript
import { cache } from 'react';

export const getUser = cache(async (id) => {
  const res = await fetch(`https://api.example.com/users/${id}`);
  if (!res.ok) {
    throw new Error('Failed to fetch user');
  }
  return res.json();
});

// 组件 1
async function UserProfile({ id }) {
  const user = await getUser(id); // 发出网络请求
  return <h1>{user.name}</h1>;
}

// 组件 2（在同一树中）
async function UserHeader({ id }) {
  const user = await getUser(id); // 立即返回缓存结果
  return <header>Welcome, {user.name}</header>;
}
```

在此示例中，尽管 `UserProfile` 和 `UserHeader` 都调用了 `getUser(id)`，但实际的 `fetch` 请求只会执行一次。第二次调用将接收到缓存的数据。

### 客户端行为

默认情况下，`cache` 函数在客户端没有缓存行为。它作为一个空操作（no-op），意味着它会返回原始函数，该函数将在每次调用时执行。这种设计允许你编写在服务器上使用 `cache` 的共享组件，而不会在客户端中断。但是，你必须注意，该函数在客户端环境中不会被记忆化。

### 缓存机制

`cache` 函数构建一个嵌套的映射结构来存储结果。它对对象和函数参数使用 `WeakMap`，对原始类型参数（字符串、数字、布尔值等）使用标准的 `Map`。这确保了如果对象在其他地方不再被引用，可以被垃圾回收，从而防止内存泄漏。

以下是缓存结构的简化示意图：

```d2
direction: down

call: "cachedFn(1, { id: 'a' })" {
  label: "Function Call"
  shape: oval
}

tree: "Cache Tree" {
  shape: package
  grid-columns: 1

  fn_ref: "Function Reference (WeakMap)" {
    shape: package

    fn_node: "fn -> Node 1" {
      shape: rectangle
      
      primitive_args: "Primitive Args (Map)" {
        shape: package

        primitive_node: "1 -> Node 2" {
          shape: rectangle
          
          object_args: "Object Args (WeakMap)" {
            shape: package

            result_node: "{id: 'a'} -> Node 3 (Result)" {
              shape: document
              "status: TERMINATED"
              "value: { ... }"
            }
          }
        }
      }
    }
  }
}

call -> tree.fn_ref: "Traverses tree to find or store result"

```

## 相关 API

还有一些其他 API 与 React 的缓存系统协同工作。

<x-cards data-columns="2">
  <x-card data-title="cacheSignal" data-icon="lucide:signal">
    一个返回与当前请求绑定的 `AbortSignal` 的函数。你可以将此信号传递给 fetch 请求，以便在渲染中止时自动取消它们。在客户端，它返回 `null`。
  </x-card>
  <x-card data-title="unstable_useCacheRefresh" data-icon="lucide:refresh-cw">
    一个 Hook，返回一个用于使整个缓存失效并触发更新的函数。这对于在客户端组件中实现“刷新”按钮等功能以重新获取服务器数据非常有用。
  </x-card>
</x-cards>

### 示例：使用 `cacheSignal` 和 `useCacheRefresh`

以下是如何结合使用这些 API 来实现稳健的数据获取。

**服务器数据获取函数：**

```javascript
// lib/data.js
import { cache, cacheSignal } from 'react';

export const getItems = cache(async () => {
  const signal = cacheSignal();
  const res = await fetch('https://api.example.com/items', { signal });
  return res.json();
});
```

**带刷新按钮的客户端组件：**

```javascript
'use client';

import { useTransition, unstable_useCacheRefresh as useCacheRefresh } from 'react';

export function RefreshButton() {
  const refresh = useCacheRefresh();
  const [isPending, startTransition] = useTransition();

  const handleRefresh = () => {
    startTransition(() => {
      refresh();
    });
  };

  return (
    <button onClick={handleRefresh} disabled={isPending}>
      {isPending ? 'Refreshing...' : 'Refresh Data'}
    </button>
  );
}
```

## 环境行为摘要

缓存 API 的行为在服务器和客户端环境之间有很大差异。下表总结了这些差异：

| API                          | 服务器环境                               | 客户端环境（默认）                                   |
| ---------------------------- | ------------------------------------------------ | -------------------------------------------------------------- |
| `cache(fn)`                  | 在请求期间记忆化 `fn`。     | 空操作。返回 `fn`，不带任何缓存行为。              |
| `cacheSignal()`              | 返回当前请求的 `AbortSignal`。 | 返回 `null`。                                                |
| `unstable_useCacheRefresh()` | 不适用（它是一个 Hook）。                    | 返回一个函数，用于使缓存失效并触发重新渲染。 |

理解这些区别是在跨越服务器和客户端执行的应用程序中有效使用 React 缓存功能的关键。

---

掌握了 React 的缓存机制后，你就可以构建性能更佳的服务器渲染应用程序。要探索另一个用于非阻塞 UI 更新的高级功能，请继续阅读[过渡 (Transitions)](./advanced-transitions.md) 指南。