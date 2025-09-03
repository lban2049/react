# 状态 Hook

State 是组件的记忆。它通过跟踪随时间变化的数据，使组件能够进行交互。State Hook 提供了在函数组件中声明、更新和管理这些数据的工具。本节涵盖了管理组件状态的主要 Hook，从简单值到复杂逻辑和异步操作。

有关状态变化相关的副作用，请参阅 [Effect Hook](./hooks-effect.md)。

<x-cards data-columns="2">
  <x-card data-title="useState" data-icon="lucide:variable">
    为组件添加 state 的最基础的 Hook。适用于数字、字符串或布尔值等简单 state 值。
  </x-card>
  <x-card data-title="useReducer" data-icon="lucide:git-commit-vertical">
    `useState` 的替代方案，用于管理更复杂的 state 逻辑，尤其是在下一个 state 依赖于前一个 state 时。
  </x-card>
  <x-card data-title="useActionState" data-icon="lucide:form-input">
    专为处理表单提交和其他操作、管理待定状态以及处理操作返回结果而设计。
  </x-card>
  <x-card data-title="useOptimistic" data-icon="lucide:fast-forward">
    允许你在异步操作完成前立即显示操作结果，从而提供更好的用户体验。
  </x-card>
</x-cards>


## useState

`useState` 是管理 state 最常用的 Hook。你在组件的顶层调用它来声明一个 state 变量。

```javascript
function useState<S>(
  initialState: (() => S) | S,
): [S, Dispatch<BasicStateAction<S>>]
```

**参数**

| Name | Type | Description |
|---|---|---|
| `initialState` | `S` or `() => S` | state 的初始值。它可以是任何类型的值，也可以是返回初始值的函数。该函数仅在初始渲染期间执行。 |

**返回值**

一个包含两个元素（不多不少）的数组：
1.  当前的 state 值。
2.  一个 `dispatch` 函数，可让你将 state 更新为新值并触发重新渲染。

**示例：一个简单的计数器**

```javascript
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

在此示例中，`useState(0)` 将 `count` state 变量初始化为 `0`。点击按钮会使用新值调用 `setCount`，这会更新 state 并重新渲染组件以显示新的计数值。

## useReducer

当你有涉及多个子值的复杂 state 逻辑，或者当下一个 state 依赖于前一个 state 时，通常首选 `useReducer` 而不是 `useState`。

```javascript
function useReducer<S, I, A>(
  reducer: (S, A) => S,
  initialArg: I,
  init?: I => S,
): [S, Dispatch<A>]
```

**参数**

| Name | Type | Description |
|---|---|---|
| `reducer` | `(S, A) => S` | 一个指定 state 如何更新的函数。它必须是纯函数，接收 state 和 action 作为参数，并返回下一个 state。 |
| `initialArg` | `I` | 用于计算初始 state 的值。 |
| `init` | `I => S` | (可选) 一个返回初始 state 的初始化函数。如果未提供，则 `initialArg` 将用作初始 state。 |

**返回值**

一个包含两个元素（不多不少）的数组：
1.  当前的 state 值。
2.  一个 `dispatch` 函数，你可以向其传递一个 `action` 并触发 state 更新。

**示例：管理复杂 State**

```javascript
import { useReducer } from 'react';

const initialState = {count: 0};

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return {count: state.count + 1};
    case 'decrement':
      return {count: state.count - 1};
    default:
      throw new Error();
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <>
      Count: {state.count}
      <button onClick={() => dispatch({type: 'decrement'})}>-</button>
      <button onClick={() => dispatch({type: 'increment'})}>+</button>
    </>
  );
}
```
此示例将所有 state 更新逻辑集中到 `reducer` 函数中，使组件的事件处理程序更清晰，因为它们只需要 `dispatch` action。

## useActionState

此 Hook 专为管理操作（例如表单提交）的 state 而设计。它提供有关操作的待定状态和操作完成后返回的数据的信息。

```javascript
function useActionState<S, P>(
  action: (Awaited<S>, P) => S,
  initialState: Awaited<S>,
  permalink?: string,
): [Awaited<S>, (P) => void, boolean]
```

**参数**

| Name | Type | Description |
|---|---|---|
| `action` | `(S, P) => S` | 要执行的函数。它接收先前的 state 和操作的有效负载。 |
| `initialState` | `S` | 初始 state 值。 |
| `permalink` | `string` | (可选) 用于渐进增强场景的 URL。 |

**返回值**

一个包含三个元素的数组：
1.  操作的当前 state。在调用操作之前，它是 `initialState`，在操作完成后，它是操作的返回值。
2.  一个用于调用操作的 `dispatch` 函数。
3.  一个布尔值 `isPending`，在操作执行期间为 `true`。

**示例：表单提交**
```javascript
import { useActionState } from 'react';

async function updateUser(previousState, formData) {
  // Simulate a network request
  await new Promise(res => setTimeout(res, 500)); 
  const name = formData.get('name');
  if (name.length < 3) {
    return { success: false, message: 'Name must be longer.' };
  }
  return { success: true, message: `Welcome, ${name}!` };
}

function ChangeNameForm() {
  const [state, submitAction, isPending] = useActionState(
    updateUser,
    { success: null, message: '' }
  );

  return (
    <form action={submitAction}>
      <input type="text" name="name" />
      <button type="submit" disabled={isPending}>
        {isPending ? 'Saving...' : 'Update'}
      </button>
      {state.message && <p>{state.message}</p>}
    </form>
  );
}
```

## useOptimistic

`useOptimistic` 允许你应用一个临时的、“乐观”的 state 更新，该更新会在异步操作进行期间立即呈现给用户。如果操作失败，state 会自动恢复。

```javascript
function useOptimistic<S, A>(
  passthrough: S,
  reducer: ?(S, A) => S,
): [S, (A) => void]
```

**参数**

| Name | Type | Description |
|---|---|---|
| `passthrough` | `S` | 当没有乐观更新处于活动状态时使用的默认 state 值。 |
| `reducer` | `(S, A) => S` | 一个接收当前 state 和乐观值并返回新的乐观 state 的函数。 |

**返回值**

一个包含两个元素的数组：
1.  乐观 state，除非有操作正在进行，否则它将与 `passthrough` 相同。
2.  一个 `addOptimistic` 函数，用于应用临时 state 更新。

**示例：乐观地添加消息**
```javascript
import { useOptimistic, useState, useRef } from 'react';

async function deliverMessage(message) {
  // Simulate network delay
  await new Promise(res => setTimeout(res, 1000));
  // To simulate an error, you could throw an error here.
  return message;
}

function MessageThread() {
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
    const sentMessage = await deliverMessage(message);
    setMessages(prev => [...prev, { text: sentMessage }]);
  }

  return (
    <div>
      {optimisticMessages.map((m, i) => (
        <div key={i}>{m.text}{m.sending && <small> (Sending...)</small>}</div>
      ))}
      <form action={formAction} ref={formRef}>
        <input type="text" name="message" placeholder="Hello!" />
        <button type="submit">Send</button>
      </form>
    </div>
  );
}
```
当用户发送消息时，它会立即出现在列表中，并带有一个“Sending...”标签。一旦异步 `deliverMessage` 函数完成，乐观 state 将被 `setMessages` 中的实际 state 替换。

---

现在你已经了解了如何管理 state，下一步是学习如何处理因 state 变化或组件生命周期事件而产生的副作用。继续阅读 [Effect Hook](./hooks-effect.md) 部分以了解更多信息。