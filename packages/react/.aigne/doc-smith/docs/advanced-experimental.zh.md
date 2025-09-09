# 实验性 API

欢迎来到 React 的前沿。本节介绍了可用于测试和反馈的实验性和不稳定 API。这些功能仍在开发中，其行为或签名可能会在未来版本中发生变化。我们强烈建议不要在生产环境中使用它们，除非您已准备好应对潜在的破坏性变更。

这些 API 提供了对新功能的早期访问，例如高级安全控制和更精细的渲染管理。

## Tainting：防止敏感数据泄露

在使用 React Server Components 时，防止服务器端的敏感数据意外传递给客户端至关重要。Taint API 提供了一种机制，可将某些值或对象标记为“受污染的”，如果尝试为客户端序列化这些值或对象，React 将会抛出错误。

对于构建稳健的以服务器为中心的应用来说，这是一项强大的安全功能。

### `experimental_taintUniqueValue`

此函数用于污染唯一的原始值，如机密信息、API 密钥或令牌。它有助于确保特定的字符串或数字无法离开服务器环境。

**用法**

```javascript Server Component icon=logos:react
import { experimental_taintUniqueValue } from 'react';

async function getUserData(userId) {
  const userSecret = await getSecretFromVault(userId);

  // 创建一个生命周期对象。当该对象被垃圾回收时，
  // 值的污染标记可能会被移除。
  const lifetime = {};

  // 污染这个机密值。如果该值被传递给客户端组件，
  // React 将会抛出错误。
  experimental_taintUniqueValue(
    'User secret must not be exposed to the client.',
    lifetime,
    userSecret
  );

  return { secret: userSecret };
}
```

**参数**

| Parameter | Type | Description |
|---|---|---|
| `message` | `string` | 如果被污染的值传递给客户端，则显示此错误信息。 |
| `lifetime` | `object` | 一个对象，其垃圾回收生命周期与污染标记绑定。当该对象被回收时，污染标记可能会被移除。 |
| `value` | `string \| bigint \| $ArrayBufferView` | 要污染的唯一原始值。 |


### `experimental_taintObjectReference`

此函数会污染整个对象或函数引用。它对于将数据库连接或文件句柄等复杂对象标记为仅限服务器使用非常有用。

**用法**

```javascript Server Component icon=logos:react
import { experimental_taintObjectReference } from 'react';

async function getDatabaseConnection() {
  const dbConnection = await createDbConnection();

  // 污染整个数据库连接对象。
  // 它不能被传递给客户端组件或在服务器操作中使用。
  experimental_taintObjectReference(
    'Database connection objects are server-only and cannot be serialized.',
    dbConnection
  );

  return dbConnection;
}
```

**参数**

| Parameter | Type | Description |
|---|---|---|
| `message` | `string` | 如果被污染的对象传递给客户端，则显示此错误信息。 |
| `object` | `object \| function` | 要污染的对象或函数引用。 |


## 使用 postpone 推迟渲染

The `unstable_postpone` 函数允许服务器组件声明式地暂停其渲染并等待数据，而不会阻塞服务器线程。调用该函数时，它会抛出一个特殊的信号，React 会捕获该信号。然后，React 会显示最近的 `<Suspense>` fallback，并稍后重试渲染该组件。

这对于处理未封装在基于 Promise 的 API 中的数据依赖项特别有用。

**用法**

```javascript Page with Postponed Component icon=logos:react
import { Suspense } from 'react';
import { unstable_postpone as postpone } from 'react';
import { dataCache } from './data';

function NewsFeed() {
  const articles = dataCache.get('articles');
  if (!articles) {
    // 如果缓存中没有文章，则推迟渲染。
    // React 将显示 Suspense fallback 并重试。
    postpone('News feed is not ready yet.');
  }

  return (
    <div>
      {articles.map(article => <p key={article.id}>{article.title}</p>)}
    </div>
  );
}

export default function App() {
  return (
    <Suspense fallback={<div>Loading news...</div>}>
      <NewsFeed />
    </Suspense>
  );
}
```

这提供了一种将非标准数据获取模式与 React 的流式服务器渲染功能集成的方法。

## 其他实验性 API

以下是在某些 React 构建版本中可用的其他实验性或不稳定 API 的列表。它们的用途和用法可能会有所不同。

| API | Environment | Description |
|---|---|---|
| `experimental_useOptimistic` | 客户端 | 一个用于管理乐观 UI 更新的 Hook。此后，它已在 Canary 和稳定渠道中升级为 `useOptimistic`。使用 `experimental_` 前缀会产生控制台警告。 |
| `experimental_useEffectEvent` | 客户端 | 一个提议中的 Hook，用于从 `useEffect` 中提取非响应式逻辑，以防止其不必要地重新运行。 |
| `unstable_Activity` | 客户端 | 一个用于控制屏幕外内容的可见性和状态的组件，对虚拟化列表或标签面板等功能很有用。 |
| `unstable_SuspenseList` | 客户端 / 服务器端 | 一个帮助协调多个 `<Suspense>` 边界加载顺序的组件，以创建更受控制且不那么混乱的加载体验。 |
| `unstable_ViewTransition` | 客户端 | 一个帮助协调不同视图或 UI 状态之间动画过渡的 API，与浏览器的 View Transitions API 集成。 |
| `unstable_getCacheForType` | 服务器端 | 一个仅限服务器使用的函数，用于访问给定类型的请求作用域缓存实例，在单次渲染过程中跨组件记忆化数据时非常有用。 |

---

探索这些 API 可以深入了解 React 的未来发展方向。由于它们以服务器为中心，您可能会发现以下指南对理解相关背景很有帮助。

<x-cards>
  <x-card data-title="服务器端与客户端环境" data-icon="lucide:server-cog" data-href="/advanced/server-vs-client">
    了解 React 两种渲染环境之间的根本区别及其各自的功能。
  </x-card>
  <x-card data-title="缓存" data-icon="lucide:database" data-href="/advanced/caching">
    深入了解 React 的缓存机制，这对于构建高性能的服务器渲染应用至关重要。
  </x-card>
</x-cards>