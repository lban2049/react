# State Hook

State Hook 提供了一种在函数组件中管理局部状态的方法。它允许你向组件添加状态逻辑，而无需将其转换为类组件。本节将介绍用于管理状态的主要 Hook，涵盖从简单值到复杂状态转换和表单交互的各种场景。

如需全面了解所有可用的 Hook，请参阅 [Hooks](./hooks.md) 主文档。

<x-cards data-columns="2">
  <x-card data-title="useState" data-icon="lucide:variable">
    管理简单局部状态最基础的 Hook。
  </x-card>
  <x-card data-title="useReducer" data-icon="lucide:binary">
    useState 的替代方案，用于管理更复杂的状态逻辑。
  </x-card>
  <x-card data-title="useOptimistic" data-icon="lucide:fast-forward">
    用于即时更新 UI，无需等待异步操作完成。
  </x-card>
  <x-card data-title="useActionState" data-icon="lucide:form-input">
    旨在管理表单状态及其触发的操作。
  </x-card>
</x-cards>


## useState

`useState` Hook 是为函数组件添加状态的最常用方法。调用它以声明一个状态变量，它会返回一个包含两个值的数组：当前状态和一个用于更新状态的函数。

### API 参考

```javascript
const [state, setState] = useState(initialState);
```

**参数**

| Parameter      | Type                | Description                                                                                                                              |
|----------------|---------------------|------------------------------------------------------------------------------------------------------------------------------------------|
| `initialState` | `S` or `() => S`    | 状态的初始值。如果传入一个函数，该函数将仅在初始渲染时执行，用于计算初始状态。 |

**返回值**

一个包含两个元素的数组：

| Index | Name       | Type                                | Description                                                                                                                           |
|-------|------------|-------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------|
| 0     | `state`    | `S`                                 | 当前渲染的状态值。                                                                                |
| 1     | `setState` | `Dispatch<BasicStateAction<S>>`     | 一个用于更新状态的函数。你可以直接传递一个新值，或者传递一个接收前一个状态并返回新状态的函数。 |

### 示例

这是一个使用 `useState` 来跟踪计数的简单计数器组件。

```javascript Counter Component icon=logos:javascript
import { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

## useReducer

`useReducer` 是 `useState` 的一个替代方案，更适用于管理包含多个子值的复杂状态逻辑，或者当下一个状态依赖于前一个状态时。它遵循的模式与 Redux 类似。

### API 参考

```javascript
const [state, dispatch] = useReducer(reducer, initialArg, init?);
```

**参数**

| Parameter    | Type             | Description                                                                                                     |
|--------------|------------------|-----------------------------------------------------------------------------------------------------------------|
| `reducer`    | `(S, A) => S`    | 一个接收当前状态 (`S`) 和一个操作 (`A`) 并返回新状态的函数。                 |
| `initialArg` | `I`              | 传递给 `init` 函数的初始参数，如果未提供 `init`，则为初始状态。           |
| `init`       | `(I) => S`       | （可选）一个返回初始状态的初始化函数。它允许提取复杂的状态初始化逻辑。 |

**返回值**

一个包含两个元素的数组：

| Index | Name       | Type          | Description                                                                                                   |
|-------|------------|---------------|---------------------------------------------------------------------------------------------------------------|
| 0     | `state`    | `S`           | 当前的状态值。                                                                               |
| 1     | `dispatch` | `Dispatch<A>` | 一个函数，你可以传入一个 action 来调用它，从而通过执行 reducer 函数来触发状态更新。      |

### 示例

此计数器示例经过重构，使用 `useReducer` 来处理计数的增加和减少。

```javascript Reducer Counter icon=logos:javascript
import { useReducer } from 'react';

const initialState = { count: 0 };

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 };
    case 'decrement':
      return { count: state.count - 1 };
    default:
      throw new Error();
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <div>
      <p>Count: {state.count}</p>
      <button onClick={() => dispatch({ type: 'increment' })}>+</button>
      <button onClick={() => dispatch({ type: 'decrement' })}>-</button>
    </div>
  );
}
```

## useOptimistic

`useOptimistic` 是一个用于管理乐观 UI 更新的 Hook。它允许你的界面在用户操作后立即响应，而实际的数据变更在后台异步进行。如果后台操作失败，UI 会自动恢复到之前的状态。

### API 参考

```javascript
const [optimisticState, addOptimistic] = useOptimistic(passthrough, reducer?);
```

**参数**

| Parameter     | Type                | Description                                                                                                                                 |
|---------------|---------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| `passthrough` | `S`                 | 当没有乐观更新正在进行时返回的默认状态。这通常是由 `useState` 管理或通过 props 传递的状态。 |
| `reducer`     | `?(S, A) => S`      | （可选）一个接收当前状态和一个 action，并返回新的乐观状态的函数。                                   |

**返回值**

一个包含两个元素的数组：

| Index | Name              | Type           | Description                                                                                                                   |
|-------|-------------------|----------------|-------------------------------------------------------------------------------------------------------------------------------|
| 0     | `optimisticState` | `S`            | 状态值。在异步操作期间，它将反映乐观值，否则它将是 `passthrough` 状态。     |
| 1     | `addOptimistic`   | `(A) => void`  | 一个通过传入 action 来触发乐观更新的函数。                                                            |

### 示例

想象一个聊天应用，其中新消息会立即显示，甚至在服务器确认之前。

```javascript Optimistic Chat icon=logos:javascript
import { useOptimistic, useState, useRef } from 'react';

async function sendMessage(message) {
  // Simulate a network request
  await new Promise(res => setTimeout(res, 1000));
  // For demo, let's pretend it can fail
  if (message.includes('error')) {
    throw new Error('Failed to send message');
  }
  return { text: message, sending: false };
}

function Chat() {
  const [messages, setMessages] = useState([]);
  const [optimisticMessages, addOptimisticMessage] = useOptimistic(
    messages,
    (state, newMessage) => [
      ...state,
      { text: newMessage, sending: true }
    ]
  );
  const formRef = useRef();

  async function formAction(formData) {
    const message = formData.get('message');
    addOptimisticMessage(message);
    formRef.current.reset();
    try {
      const sentMessage = await sendMessage(message);
      setMessages(prev => [...prev, sentMessage]);
    } catch (e) {
      // The UI will automatically revert on error
      console.error(e);
    }
  }

  return (
    <div>
      {optimisticMessages.map((m, i) => (
        <div key={i}>{m.text} {m.sending && <small>(Sending...)</small>}</div>
      ))}
      <form action={formAction} ref={formRef}>
        <input type="text" name="message" />
        <button type="submit">Send</button>
      </form>
    </div>
  );
}
```

## useActionState

`useActionState` 是一个用于管理表单操作状态的 Hook。它提供操作的待定状态以及操作完成后返回的数据。

### API 参考

```javascript
const [state, dispatch, isPending] = useActionState(action, initialState, permalink?);
```

**参数**

| Parameter      | Type                | Description                                                                                                   |
|----------------|---------------------|---------------------------------------------------------------------------------------------------------------|
| `action`       | `(S, P) => S`       | 要执行的函数。它接收前一个状态和操作的载荷（例如，表单数据）。     |
| `initialState` | `S`                 | 初始状态值。                                                                                      |
| `permalink`    | `?string`           | （可选）用于服务器端表单提交后重定向的 URL。                                             |

**返回值**

一个包含三个元素的数组：

| Index | Name        | Type           | Description                                                                             |
|-------|-------------|----------------|-----------------------------------------------------------------------------------------|
| 0     | `state`     | `S`            | 操作的当前状态。它持有上次操作执行后返回的值。 |
| 1     | `dispatch`  | `(P) => void`  | 传递给 `<form>` 的 `action` 属性以触发操作的函数。               |
| 2     | `isPending` | `boolean`      | 一个布尔值，当操作正在进行时为 `true`，否则为 `false`。             |

### 示例

此示例展示了一个用于更新用户名的表单。`useActionState` Hook 管理待定状态以及从服务器操作返回的任何错误消息。

```javascript Action State Form icon=logos:javascript
import { useActionState } from 'react';

async function updateName(previousState, formData) {
  const newName = formData.get('name');
  if (newName.length < 3) {
    return { error: 'Name must be at least 3 characters long.' };
  }
  // Simulate async update
  await new Promise(res => setTimeout(res, 500));
  return { error: null, success: `Name changed to ${newName}` };
}

function ChangeNameForm() {
  const [state, formAction, isPending] = useActionState(updateName, { error: null });

  return (
    <form action={formAction}>
      <label htmlFor="name">Name:</label>
      <input id="name" name="name" />
      <button type="submit" disabled={isPending}>
        {isPending ? 'Updating...' : 'Update'}
      </button>
      {state?.error && <p style={{ color: 'red' }}>{state.error}</p>}
      {state?.success && <p style={{ color: 'green' }}>{state.success}</p>}
    </form>
  );
}

```

---

这些 State Hook 是在 React 中创建动态和交互式组件的基础。掌握了状态管理之后，下一步是管理副作用，例如获取数据或设置订阅。请继续阅读 [Effect Hook](./hooks-effect.md) 部分以了解更多信息。
