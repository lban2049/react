# 服务器环境

`react/server` 入口点专为服务器端环境（例如使用 React Server Components 时）而设计。它提供了一套精选的 React API，这些 API 经过优化，可确保在服务器上安全执行，同时还具备强大的数据缓存和安全功能。

该包排除了依赖浏览器环境和交互性的仅限客户端的 API（如 `useState` 或 `useEffect`）。它专注于渲染、数据获取模式，并防止敏感数据被无意中暴露给客户端。

## 可用 API

以下 API 从 `react/server` 包中导出。其中许多 API 与标准 `react` 包中的 API 相同，但它们保证可以安全地在服务器上使用。

| Export                 | Type                      | Description                                                                                                   |
| ------------------------ | ------------------------- | ------------------------------------------------------------------------------------------------------------- |
| `Children`               | Object                    | 用于处理 `props.children` 的工具函数（`map`、`forEach`、`count`、`toArray`、`only`）。                   |
| `Fragment`               | Component                 | 一个用于渲染多个元素而无需包装器 DOM 节点的组件。                                       |
| `Profiler`               | Component                 | 在开发模式下测量 React 树的渲染性能。                                                  |
| `StrictMode`             | Component                 | 一个用于高亮显示应用程序中潜在问题的工具（仅限开发模式）。                                      |
| `Suspense`               | Component                 | 让你可以在其子组件加载完成前显示一个后备 UI。对于数据获取至关重要。         |
| `cache`                  | Function                  | 一个仅限服务器使用的函数，用于记忆化数据获取函数。                                                    |
| `cacheSignal`            | Function                  | 与 `cache` 结合使用，以进行更高级的缓存管理。                                          |
| `cloneElement`           | Function                  | 以一个元素为起点，克隆并返回一个新的 React 元素。                                |
| `createElement`          | Function                  | 创建并返回一个给定类型的新 React 元素。                                                    |
| `createRef`              | Function                  | 创建一个 ref 对象。                                                                                         |
| `forwardRef`             | Function                  | 让组件可以通过 ref 将一个 DOM 节点暴露给其父组件。                                        |
| `isValidElement`         | Function                  | 验证对象是否为 React 元素。                                                                    |
| `lazy`                   | Function                  | 让你能够延迟加载组件代码，直到它首次渲染。                             |
| `memo`                   | Function                  | 一个用于记忆化组件渲染输出的高阶组件。                                           |
| `use`                    | Hook                      | 一个用于读取资源（如 Promise 或 context）值的 Hook。                                        |
| `useId`                  | Hook                      | 一个用于生成在服务器和客户端之间保持稳定的唯一 ID 的 Hook。                                |
| `useCallback`            | Hook                      | 返回一个记忆化的回调函数。                                                                         |
| `useDebugValue`          | Hook                      | 可用于在 React 开发者工具中为自定义 Hook 显示标签。                                            |
| `useMemo`                | Hook                      | 返回一个记忆化的值。                                                                                     |
| `version`                | String                    | React 的当前版本。                                                                                 |
| `captureOwnerStack`      | Function                  | 一个仅限开发模式使用的工具，用于捕获组件栈。                                                         |

## 服务器端特有功能

除了标准 API，服务器环境还引入了专为性能和安全设计的特有功能。

### 使用 `cache` 进行缓存

`cache` 函数是在 React Server Components 中优化数据获取的一个关键工具。它包装一个数据获取函数（例如数据库查询或 API 调用）并记忆化其结果。这可以确保在单次服务器渲染过程中，如果使用相同的参数多次调用同一个函数，底层的数据获取操作只会执行一次。

```javascript Server-Side Data Fetching icon=logos:javascript
import { cache } from 'react/server';
import db from './database';

export const getUser = cache(async (id) => {
  console.log(`Fetching user ${id} from the database...`);
  const user = await db.users.find({ where: { id } });
  return user;
});

async function UserProfile({ id }) {
  const user = await getUser(id);
  return <div>{user.name}</div>;
}

async function UserAvatar({ id }) {
  // 如果在同一次渲染中以与 UserProfile 相同的 `id` 调用，
  // 此调用将被记忆化，不会触发新的数据库查询。
  const user = await getUser(id);
  return <img src={user.avatarUrl} alt={user.name} />;
}
```

### 使用数据污染保障安全

数据污染是一种安全机制，旨在防止敏感信息从服务器泄漏到客户端。当你“污染”一个值时，你将其标记为不安全，无法序列化并发送到客户端组件或在 Server Action 闭包中使用。如果 React 检测到序列化被污染值的尝试，它将抛出错误，从而保护你的应用程序免受潜在漏洞的威胁。

此功能通过 `enableTaint` 功能标志启用。

#### `taintUniqueValue(message, lifetime, value)`

此函数污染一个唯一的原始值，如秘密令牌或密码哈希。它专为全局唯一且绝不应暴露的值而设计。

<x-field data-name="message" data-type="string" data-required="false" data-desc="如果被污染的值发生泄漏，将显示的可选错误信息。"></x-field>
<x-field data-name="lifetime" data-type="object" data-required="true" data-desc="持有该值的对象。当此对象被垃圾回收时，污染标记将被移除。"></x-field>
<x-field data-name="value" data-type="string | bigint | ArrayBufferView" data-required="true" data-desc="要污染的唯一原始值或二进制值。"></x-field>

```javascript Tainting a Secret Token icon=logos:javascript
import { taintUniqueValue } from 'react/experimental'; // 实际导入路径可能有所不同

async function getUserSession(request) {
  const session = await getSessionFromCookies(request.cookies);
  const secretToken = session.internalToken;

  // `session` 对象定义了污染标记的生命周期。
  taintUniqueValue(
    'Internal session token should not be sent to the client.',
    session,
    secretToken
  );

  return session;
}
```

#### `taintObjectReference(message, object)`

此函数污染整个对象或函数引用。这对于将复杂对象（如数据库连接实例或包含多个秘密的配置对象）标记为不安全、禁止传递给客户端非常有用。

<x-field data-name="message" data-type="string" data-required="false" data-desc="如果被污染的对象发生泄漏，将显示的可选错误信息。"></x-field>
<x-field data-name="object" data-type="object | function" data-required="true" data-desc="要污染的对象或函数引用。"></x-field>

```javascript Tainting a Database Connection icon=logos:javascript
import { taintObjectReference } from 'react/experimental'; // 实际导入路径可能有所不同

function getDBConnection() {
  const dbConnection = createConnection(process.env.DATABASE_URL);

  // 将整个连接对象标记为不安全，禁止传递给客户端。
  taintObjectReference('Database connection cannot be serialized.', dbConnection);

  return dbConnection;
}
```

## 内部导出

服务器环境还导出了 `__SERVER_INTERNALS_DO_NOT_USE_OR_WARN_USERS_THEY_CANNOT_UPGRADE`。顾名思义，这是一个供 React 及其相关库使用的内部对象。它提供了对共享服务器状态（包括数据污染的注册表）的访问权限。应用程序开发者绝不应直接使用此对象，因为它不稳定且可能随时更改，恕不另行通知。

---

通过提供这些专用工具，`react/server` 环境使开发者能够使用 React 构建高性能且安全的服务器渲染应用程序。有关通用 API 的更多详情，请参阅完整的 [API 参考](./api-reference.md)。