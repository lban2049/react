# 实验性 API

**警告：本页记录的 API 是实验性的，尚未在稳定版本中提供。在未来的 React 版本中，它们可能会发生重大变化或被完全移除。**

本节概述了正在积极开发的实验性功能。它们旨在供早期采用者和库作者进行实验并提供反馈。我们强烈建议不要在生产应用程序中使用这些 API。

## 安全性：Tainting API

在服务器环境中，防止敏感数据（如 API 密钥或用户会话令牌）被意外传递到客户端代码至关重要。Tainting API 是一项仅限服务器使用的功能，旨在创建安全边界，如果一个“受污染的”值被序列化并发送到客户端，就会抛出错误。

在使用 React Server Components 或 Server Actions 时，此机制有助于防止数据泄漏。

### `experimental_taintUniqueValue(message, lifetime, value)`

此函数会污染一个唯一的原始值，如机密令牌或密钥。React 将阻止此特定值被传递给任何 Client Component 或 Server Action 闭包。

**参数**

| Name | Type | Description |
|---|---|---|
| `message` | `string` | 违反污染规则时抛出的可选自定义错误消息。 |
| `lifetime` | `object` | 一个对象引用。当此对象被垃圾回收时，污染将被移除。这有助于管理污染注册表使用的内存。 |
| `value` | `string` \| `bigint` \| `$ArrayBufferView` | 要污染的唯一的、敏感的原始值。 |

**示例：污染用户的 API 密钥**

```javascript
// 位于一个仅限服务器的文件中
import { experimental_taintUniqueValue } from 'react';
import { getUserData } from './database';

export async function getTaintedUserData(userId) {
  const user = await getUserData(userId);

  // 此对象的生命周期与请求绑定
  const requestLifetime = {}; 

  // 污染用户的机密 API 密钥
  experimental_taintUniqueValue(
    'API key must not be exposed to the client.',
    requestLifetime,
    user.apiKey
  );

  return user;
}

// 在一个 Server Component 中：
async function UserProfile({ userId }) {
  const user = await getTaintedUserData(userId);

  // 这是安全的，因为 `user.apiKey` 没有被传递给客户端。
  const serverSideData = await fetchDataWithKey(user.apiKey);

  return (
    // 如果你将 `user.apiKey` 传递给 ClientInfo，React 将会抛出错误。
    <ClientInfo name={user.name} />
  );
}
```

### `experimental_taintObjectReference(message, object)`

此函数会污染整个对象或函数引用。任何序列化此对象并将其发送到客户端的尝试都将导致错误。

**参数**

| Name | Type | Description |
|---|---|---|
| `message` | `string` | 违反污染规则时抛出的可选自定义错误消息。 |
| `object` | `object` \| `function` | 要污染的对象或函数引用。 |

**示例：污染数据库连接**

```javascript
// 位于一个仅限服务器的文件中
import { experimental_taintObjectReference } from 'react';
import { createDbConnection } from './db';

const db = createDbConnection();

// 污染数据库连接对象，以防止其离开服务器。
experimental_taintObjectReference(
  'The database connection object cannot be sent to the client.',
  db
);

export default db;
```

## 渲染：`postpone(reason)`

`postpone` 函数允许 React Server Component 中断其渲染过程而不会导致服务器错误。当被调用时，它会向 React 渲染器发出信号，表明组件尚未准备好渲染，并且渲染应在稍后重试。这对于数据尚不可用，而你更愿意等待而不是渲染 `Suspense` 回退的场景很有用。

它的工作原理是抛出一个特殊的对象，渲染器会捕获该对象并将其解释为暂停的信号。

**参数**

| Name | Type | Description |
|---|---|---|
| `reason` | `string` | 一个描述性字符串，解释渲染被推迟的原因。这用于调试。 |

**示例：为个性化问候语推迟渲染**

```javascript
import { postpone } from 'react';
import { getPersonalizedContent } from './contentApi';

async function PersonalizedGreeting({ userId }) {
  // 获取个性化内容，初始生成可能较慢。
  const content = await getPersonalizedContent(userId);

  if (content.status === 'PENDING') {
    // 如果内容尚未准备好，则推迟渲染。
    // React 将保持连接并重试渲染此组件。
    postpone(`Personalized content for user ${userId} is not ready.`);
  }

  return <h1>{content.greeting}</h1>;
}
```

---

这些实验性 API 为构建安全和动态的应用程序提供了强大的新功能。随着它们的成熟，它们可能会被集成到稳定的 React API 中。目前，请使用它们进行探索和提供反馈。对于生产就绪的功能，请查阅主 [API 参考](./api-reference.md)。