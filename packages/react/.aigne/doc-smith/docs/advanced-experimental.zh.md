# 实验性 API

本指南涵盖了 React 中的实验性和不稳定的 API。这些 API 旨在用于测试和反馈。它们可能会在未来的版本中发生重大变化或被移除，而不会遵循通常的弃用周期。请谨慎使用它们，尤其是在生产环境中。

对于更稳定的高级功能，你可能对 [服务器与客户端环境](./advanced-server-vs-client.md) 或 [缓存](./advanced-caching.md) 感兴趣。

## 使用 Taint API 实现服务器端安全

在使用 React 服务器组件构建应用程序时，防止敏感的仅服务器数据意外传递给客户端至关重要。Taint API 提供了一种机制，可以将特定数据标记为“已污染”，如果 React 尝试在发送给客户端的有效负载中序列化此数据，则会引发错误。

此安全功能仅在服务器环境中可用。

```d2
direction: down

Server: {
  "服务器组件": {
    "1. 污染敏感数据": {
      shape: step
      "experimental_taintUniqueValue('api_key', ...)"
    }
    "2. 为客户端组件准备 props" : { shape: step }
  }
}

"序列化边界": {
  shape: hexagon
  "3. 检查污染值"
}

Client: {
  "客户端组件"
}

Server -> "序列化边界": "传递 props"

subgraph {
  direction: right
  "序列化边界" -- "数据是干净的" --> Client: "4a. 发送有效负载"
  "序列化边界" -- "检测到污染值" --> Server: "4b. 抛出错误（已防止泄漏）" {
    style.stroke: red
  }
}
```

### `experimental_taintUniqueValue`

此函数污染一个唯一的原始值，例如密钥或令牌。它可以与字符串、bigint 和 ArrayBuffer 视图一起使用。

**参数**

| Name      | Type                                | Description                                                                                                                            |
|-----------|-------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `message` | `string` (optional)                 | 如果被污染的值被序列化，将抛出的自定义错误信息。默认为通用警告。                                                                       |
| `lifetime`| `object`                            | 一个持有该值的对象。污染在该对象的生命周期内被认为是有效的。                                                                           |
| `value`   | `string` \| `bigint` \| `ArrayBufferView` | 要污染的唯一的、敏感的值。它不能是通用对象或函数。                                                                                     |

**示例**

```javascript
// 在服务器组件或服务器操作中
import { experimental_taintUniqueValue } from 'react';

async function processUserData(user) {
  const userSecrets = { apiKey: process.env.USER_API_KEY };

  // 污染 API 密钥，以防止其离开服务器。
  // 'userSecrets' 对象定义了污染的生命周期。
  experimental_taintUniqueValue(
    'API key should not be sent to the client.',
    userSecrets,
    userSecrets.apiKey
  );

  // ……如果 userSecrets 被传递给客户端组件，React 将会抛出错误。
}
```

### `experimental_taintObjectReference`

此函数污染整个对象或函数引用。这对于像数据库连接或会话对象这样不应被序列化的东西很有用。

**参数**

| Name      | Type                  | Description                                                                                             |
|-----------|-----------------------|---------------------------------------------------------------------------------------------------------|
| `message` | `string` (optional)   | 如果被污染的对象被序列化，将抛出的自定义错误信息。默认为通用警告。                                      |
| `object`  | `object` \| `function`  | 要污染的对象或函数引用。                                                                                |

**示例**

```javascript
// 在服务器端模块中
import { experimental_taintObjectReference } from 'react';

// 假设这是你的数据库连接池
const dbConnection = createDatabaseConnection();

// 污染整个连接对象。
experimental_taintObjectReference('Database connection cannot be serialized.', dbConnection);

export function getData() {
  // 这个函数可以在服务器上安全地使用 dbConnection。
  // 但如果它或连接被传递到客户端，React 将会抛出错误。
  return dbConnection.query('SELECT * FROM users');
}
```

## 使用 unstable_postpone 实现声明式渲染延迟

unstable_postpone 函数允许服务器组件以声明方式暂停其渲染。调用该函数时，React 会停止当前的渲染过程，并等待新的渲染过程启动，届时它将尝试从根组件重新渲染。在某些渲染先决条件尚未满足，而你又希望等待而不是显示 `Suspense` 回退的情况下，这可能很有用。

**用法**

`unstable_postpone` 使用一个字符串参数进行调用，该参数提供了延迟的原因。此原因用于调试目的。

```javascript
import { unstable_postpone as postpone } from 'react';

function FeatureGate({ featureFlag }) {
  if (!featureFlag.isLoaded) {
    // 如果功能标志数据尚未准备好，则推迟渲染此树。
    // React 将会等待并稍后重试渲染。
    postpone('Feature flags are not loaded yet.');
  }

  if (!featureFlag.isEnabled) {
    return null; // 如果功能被禁用，则不进行渲染
  }

  return <MyNewFeature />;
}
```

## 其他实验性 API

还有其他一些 API 在 `experimental` 或 `unstable` 前缀下可用。它们提供了对仍在积极开发中的新功能的访问。

| API                                | Description                                                                                 |
|------------------------------------|---------------------------------------------------------------------------------------------|
| `experimental_useOptimistic`       | `useOptimistic` 的旧别名。现已稳定，使用此 API 将产生开发者警告。                          |
| `unstable_Activity`                | 将组件的作用域限定在过渡（transition）中，防止在作用域外显示回退（fallback）。              |
| `unstable_SuspenseList`            | 协调多个 `Suspense` 边界的加载顺序。                                                          |
| `unstable_ViewTransition`          | 一个用于管理 SPA 导航的 CSS 视图过渡（View Transitions）的组件。                              |
| `unstable_startGestureTransition`  | 专门为基于手势的交互启动一个过渡。                                                          |
| `unstable_useCacheRefresh`         | 提供一种刷新 React 缓存中数据的机制。                                                       |
| `unstable_getCacheForType`         | 一个仅限服务器的 API，用于访问特定类型的缓存实例。                                            |

---

通过探索这些 API，你可以一窥 React 未来的发展方向并提供宝贵的反馈。但是，在使用它们时，请务必为重大变更做好准备。

接下来，你可以在 [服务器与客户端环境](./advanced-server-vs-client.md) 中了解更多关于 React 如何处理不同环境的信息。