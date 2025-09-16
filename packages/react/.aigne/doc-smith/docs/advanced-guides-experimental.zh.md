# 实验性 API

本节提供 React 实验性版本中不稳定的 API 和功能的文档。这些 API 可能会在未来的版本中发生更改或被移除，且不会遵循标准的弃用周期。请谨慎使用，主要用于测试和反馈目的，并避免在生产环境中使用它们。

这些功能处于 React 开发的最前沿，让我们得以一窥其未来的能力。

## Hook

### `experimental_useOptimistic`

`experimental_useOptimistic` Hook 提供了一种将临时的“乐观”状态变更应用于 UI 的方法。这对于预期会成功的操作非常有用，它允许 UI 在实际的异步操作在后台完成时立即更新。如果操作失败，UI 可以恢复到之前的状态。

**注意：** 在近期的 Canary 版本中，`useOptimistic` 已被稳定。你应该移除 `experimental_` 前缀。提供此别名是为了向后兼容，并将在未来的版本中移除。

**签名**

```typescript
function experimental_useOptimistic<S, A>(
  passthroughState: S,
  updateFn: (currentState: S, optimisticValue: A) => S
): [S, (optimisticValue: A) => void];
```

**示例**

```javascript MessageForm.js icon=logos:javascript
import { experimental_useOptimistic as useOptimistic } from 'react';

function MessageForm({ messages, sendMessage }) {
  const [optimisticMessages, addOptimisticMessage] = useOptimistic(
    messages,
    (currentState, newMessage) => [
      ...currentState,
      { text: newMessage, sending: true },
    ]
  );

  async function formAction(formData) {
    const message = formData.get('message');
    addOptimisticMessage(message);
    await sendMessage(message); // Asynchronously send the message
  }

  return (
    <div>
      {optimisticMessages.map((msg, index) => (
        <div key={index}>
          {msg.text}
          {msg.sending && <small> (Sending...)</small>}
        </div>
      ))}
      <form action={formAction}>
        <input type="text" name="message" placeholder="Type a message..." />
        <button type="submit">Send</button>
      </form>
    </div>
  );
}
```
在此示例中，当用户发送消息时，该消息会立即出现在列表中，并带有一个“(Sending...)”指示器。一旦 `sendMessage` promise 被解析，React 将使用来自服务器的最终数据更新状态，移除乐观消息并用已确认的消息替换它。

### `experimental_useEffectEvent`

这个实验性的 Hook 旨在从 `useEffect` 中提取非响应式逻辑。它会创建一个具有稳定身份标识的函数，该函数可以在 effect 内部调用，而无需包含在其依赖项数组中。这可以防止在事件处理程序的逻辑发生变化时 effect 不必要地重新运行，而这是常见的 bug 来源。

**签名**

```typescript
function experimental_useEffectEvent<T extends Function>(callback: T): T;
```

**示例**

```javascript ChatRoom.js icon=logos:javascript
import { useEffect, useState, experimental_useEffectEvent as useEffectEvent } from 'react';

function ChatRoom({ roomId, theme }) {
  const [messages, setMessages] = useState([]);

  // The onMessage function's logic depends on `theme`,
  // but we don't want the effect to re-subscribe when `theme` changes.
  const onMessage = useEffectEvent(message => {
    console.log(`New message with ${theme} theme: ${message}`);
    setMessages(prev => [...prev, message]);
  });

  useEffect(() => {
    const connection = createConnection(roomId);
    connection.on('message', onMessage);

    return () => connection.disconnect();
    // The effect only re-runs if `roomId` changes.
  }, [roomId]);

  return <div>...</div>;
}

```

## 过渡与 UI

这些 API 提供了对 UI 过渡和组件可见性更精细的控制。

| API | Description |
| --- | --- |
| `unstable_ViewTransition` | 一个帮助协调视图过渡的组件，通常与原生的 CSS View Transitions API 一起使用。 |
| `unstable_startGestureTransition` | `startTransition` 的一个特殊版本，用于处理由用户手势（如拖动、滑动）发起的更新，帮助 React 对其进行适当的优先级排序。 |
| `unstable_addTransitionType` | 将一个字符串标识符与一个正在进行的过渡关联起来。这可以被其他功能（如 `unstable_ViewTransition`）用来应用特定的样式或行为。它必须在 `startTransition` 回调中调用。 |
| `unstable_Activity` | 一个控制离屏内容可见性的组件，允许你在组件当前不可见时仍保持其状态。 |
| `unstable_SuspenseList` | 一个协调多个 `<Suspense>` 边界加载顺序的组件，允许你控制内容如何显示（例如，`forwards`、`together`）。 |

**示例：使用 `unstable_addTransitionType`**

```javascript icon=logos:javascript
import { startTransition, unstable_addTransitionType } from 'react';

function navigate(url) {
  startTransition(() => {
    // 表明此状态更新是 'navigation' 过渡的一部分。
    unstable_addTransitionType('navigation');
    // 更新状态以触发页面更改。
    setCurrentPage(url);
  });
}
```

## 渲染与缓存

这些 API 提供了对渲染过程和 React 缓存机制的底层控制。

| API | Description |
| --- | --- |
| `unstable_postpone` | 一个在渲染期间调用时，会告知 React 跳过渲染当前组件树并显示 `<Suspense>` 回退内容的函数。这对于在数据或资源尚不可用时降低渲染优先级非常有用。 |
| `unstable_useCacheRefresh` | 一个返回函数的 Hook，该函数用于使 React Cache 失效并触发重新渲染。这对于需要重新获取或刷新数据的数据获取模式至关重要。 |
| `unstable_getCacheForType` | 一个用于根据提供的类型（例如，函数或上下文）访问特定缓存实例的底层函数。 |

**示例：使用 `unstable_postpone`**

```javascript icon=logos:javascript
import { Suspense } from 'react';
import { unstable_postpone as postpone } from 'react';

function ProfileDetails({ userPromise }) {
  try {
    const user = userPromise.read(); // 从基于 promise 的资源中读取数据
    return <h1>{user.name}</h1>;
  } catch (promise) {
    if (typeof promise.then === 'function') {
      // 如果数据仍在加载中，则挂起。
      throw promise;
    } else {
      // 如果存在不同类型的错误，则推迟渲染。
      postpone('Failed to fetch user details.');
    }
  }
}

function App() {
  return (
    <Suspense fallback={<div>Loading or postponed...</div>}>
      <ProfileDetails />
    </Suspense>
  );
}
```

## 服务器端安全

这些仅限服务器的函数是安全功能，旨在防止敏感数据从服务器组件意外传递到客户端组件。

### `experimental_taintUniqueValue`

污染一个原始值，例如密钥或令牌。如果一个被污染的值作为 prop 传递给客户端组件，React 将抛出一个错误。

### `experimental_taintObjectReference`

污染一个对象或函数引用，例如数据库连接或仅限服务器的实用工具。这可以防止服务器端的句柄和逻辑泄漏到客户端。

**示例**

```javascript UserData.server.js icon=logos:javascript
// 这是一个服务器组件
import { experimental_taintUniqueValue, experimental_taintObjectReference } from 'react';
import { getDbConnection } from './database';
import ClientComponent from './ClientComponent';

async function UserData({ userId }) {
  const apiKey = process.env.API_KEY;
  const db = getDbConnection();

  // 污染敏感数据以防止其离开服务器。
  experimental_taintUniqueValue('Do not expose API Key to the client', apiKey);
  experimental_taintObjectReference('Database connection cannot be passed to client', db);

  const userData = await db.query(`SELECT id, name FROM users WHERE id = ${userId}`);

  // 这是安全的：只传递可序列化、未被污染的数据。
  return <ClientComponent user={userData} />;

  // 这将抛出一个错误，因为 apiKey 已被污染：
  // return <ClientComponent user={userData} apiKey={apiKey} />;
}
```