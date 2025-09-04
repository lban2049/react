# 实验性 API

本节介绍 React 中可用的实验性和不稳定的 API。这些功能旨在供社区测试和提供反馈。它们可能会在不另行通知的情况下发生变更，不应在生产环境中使用。

> **警告：**实验性 API 可能存在错误、经历重大变更或在未来版本中被完全移除。使用它们需要您自担风险。

## 用于安全的 Taint API (仅限服务器端)

Taint API 是一种安全功能，旨在防止敏感数据从服务器环境传递到客户端。当一个值被“污染 (tainted)”时，如果你试图将其包含在客户端组件的 props 中或服务器操作的闭包中，React 将会抛出错误，从而防止意外的数据泄露。

这些 API 仅在 React 的服务器环境中使用。

### `experimental_taintUniqueValue(message, lifetime, value)`

此函数会污染一个唯一的原始值，例如密钥或用户特定的令牌。它确保此特定值不能被序列化到客户端。

**参数**

| Name | Type | Description |
|---|---|---|
| `message` | `string` | 当尝试序列化被污染的值时，将显示的可选错误信息。 |
| `lifetime`| `object` | 一个对象，其垃圾回收生命周期与被污染的值绑定。当此对象被垃圾回收时，污染状态将被移除。 |
| `value` | `string` \| `bigint` \| `ArrayBufferView` | 要污染的唯一原始值。它不能是普通的对象或函数。 |

**示例**

```javascript
// 在服务器端数据获取函数中
import { experimental_taintUniqueValue } from 'react';

async function getUserData(userId) {
  const user = await db.users.find({ id: userId });
  const apiSecret = user.apiSecret; // 一个敏感值

  // 污染密钥。user 对象用于生命周期管理。
  experimental_taintUniqueValue(
    '不要将 API 密钥泄露给客户端。',
    user,
    apiSecret
  );

  return user;
}

// 如果之后你尝试将 apiSecret 传递给客户端组件，React 将会抛出错误。
```

### `experimental_taintObjectReference(message, object)`

此函数会污染整个对象或函数引用。这对于污染那些绝不应离开服务器的复杂对象（如数据库连接或配置实例）非常有用。

**参数**

| Name | Type | Description |
|---|---|---|
| `message` | `string` | 当尝试序列化被污染的对象引用时，将显示的可选错误信息。 |
| `object` | `object` \| `function` | 要污染的对象或函数引用。它不能是字符串或数字等原始值。 |

**示例**

```javascript
// 在服务器端模块中
import { experimental_taintObjectReference } from 'react';

// 假设 dbConnection 是一个活动的数据库连接对象
const dbConnection = createDatabaseConnection();

// 污染整个数据库连接对象，以防止其被传递到客户端。
experimental_taintObjectReference('数据库连接不能发送到客户端。', dbConnection);

export function getDB() {
  return dbConnection;
}
```

## `postpone(reason)` (仅限服务器端)

`postpone` 函数允许你以给定的原因中断当前的服务器渲染。与等待 promise 解析的 `Suspense` 不同，`postpone` 是一个有意停止渲染组件子树的选择。React 可能会在稍后重试渲染。这对于推迟依赖于缓慢或非关键数据的 UI 非必要部分非常有用。

它仅在 React 的服务器环境中使用。

**参数**

| Name | Type | Description |
|---|---|---|
| `reason` | `string` | 一个字符串，用于解释渲染被推迟的原因。 |

**示例**

```javascript
import { postpone } from 'react';
import { fetchOptionalWidgetData } from './api';

async function OptionalWidget() {
  const data = await fetchOptionalWidgetData();
  if (!data) {
    // 如果数据不可用，暂时不要渲染此组件。
    // 这不会阻塞初始页面加载。
    postpone('可选的小部件数据此时不可用。');
  }

  return <div>{data.content}</div>;
}
```

## 其他不稳定和实验性 API

其他一些 API 在 `unstable_` 或 `experimental_` 前缀下可用。这些 API 暴露出来供框架和库进行测试。

| API 名称 | 描述 |
|---|---|
| `experimental_useOptimistic` | `useOptimistic` 的旧别名。它现在处于 Canary 版本中，应改用 `useOptimistic`。该前缀将被移除。 |
| `unstable_Activity` | 一个用于控制屏幕外内容可见性的组件，常用于框架中的 keep-alive 缓存等功能。 |
| `unstable_SuspenseList` | 一个用于协调多个 `Suspense` 边界外观的组件，防止内容加载时出现突兀的 UI。 |
| `unstable_ViewTransition` | 一个帮助在不同 UI 状态或视图之间创建平滑视觉过渡的 API。 |
| `unstable_getCacheForType` | 一种访问特定类型共享缓存的机制，主要用于服务器上的数据缓存。 |
| `unstable_useCacheRefresh` | 一个提供函数来使 React 缓存失效的 Hook，从而触发缓存数据的刷新。 |

---

以上是 React 实验性 API 的概述。虽然它们让我们得以一窥未来的功能，但在生产应用中应始终优先使用稳定的 API。有关服务器和客户端差异的更多详细信息，请参阅 [服务器与客户端环境](./advanced-server-vs-client.md) 指南。