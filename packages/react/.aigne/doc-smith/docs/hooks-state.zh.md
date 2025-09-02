# State Hook

State Hook 允许你向函数组件添加有状态的逻辑，使其能够记住信息并响应变化重新渲染。本节介绍了管理组件 state 的主要 Hook，从简单的值到复杂的状态转换和表单交互。

要了解 React 中的 state 概念，你可能需要回顾一下 [组件和 Props](./core-apis-components-and-props.md) 指南中的核心概念。

<x-cards data-columns="2">
  <x-card data-title="useState" data-icon="lucide:variable">
    用于基础的 state 管理，例如数字、字符串或布尔值。
  </x-card>
  <x-card data-title="useReducer" data-icon="lucide:workflow">
    适用于管理具有多个子值的复杂 state 逻辑，或下一个 state 依赖于前一个 state 的情况。
  </x-card>
  <x-card data-title="useOptimistic" data-icon="lucide:fast-forward">
    用于通过在底层异步操作完成前立即更新 UI 来创建响应迅速的用户界面。
  </x-card>
  <x-card data-title="useActionState" data-icon="lucide:form-input">
    专为处理表单提交而设计，提供对表单的 pending 状态和 action 结果的访问。
  </x-card>
</x-cards>

## useState

`useState` 是为函数组件添加 state 最常用的 Hook。它声明一个“state 变量”，并返回一个包含两个值的数组：当前 state 和一个让你更新它的函数。

### 签名
`useState<S>(initialState: (() => S) | S): [S, Dispatch<BasicStateAction<S>>]`

### 参数

| Parameter | Type | Description |
| --- | --- | --- |
| `initialState` | `S` or `(() => S)` | state 的初始值。它可以是任何类型的值，也可以是一个返回初始值的函数。该函数仅在初始渲染期间执行。 |

### 返回值
一个包含两个元素的数组：

| Index | Name | Type | Description |
| --- | --- | --- | --- |
| 0 | `state` | `S` | state 的当前值。在首次渲染时，它将等于 `initialState`。 |
| 1 | `setState` | `Dispatch<BasicStateAction<S>>` | setter 函数。你可以调用它来更新 state，这将触发组件的重新渲染。它接受一个新值或一个接收前一个 state 的函数。 |

### 示例
这是一个简单的计数器组件，使用 `useState` 来跟踪点击次数。

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
在此示例中，`useState(0)` 将 `count` state 初始化为 `0`。每次点击按钮时，都会调用 `setCount(count + 1)`，更新 state 并导致组件使用新的计数值重新渲染。

## useReducer

`useReducer` 是 `useState` 的替代方案，用于管理更复杂的 state 逻辑。当你有涉及多个子值的复杂 state 逻辑，或者下一个 state 依赖于前一个 state 时，它特别有用。

### 签名
`useReducer<S, I, A>(reducer: (S, A) => S, initialArg: I, init?: I => S): [S, Dispatch<A>]`

### 参数

| Parameter | Type | Description |
| --- | --- | --- |
| `reducer` | `(S, A) => S` | 一个纯函数，接受当前 state 和一个 action，并返回新的 state。 |
| `initialArg` | `I` | 用于计算初始 state 的值。 |
| `init` | `(I) => S` | *可选。* 一个返回初始 state 的初始化函数。如果未提供，`initialArg` 将用作初始 state。 |

### 返回值
一个包含两个元素的数组：

| Index | Name | Type | Description |
| --- | --- | --- | --- |
| 0 | `state` | `S` | 当前 state 值。 |
| 1 | `dispatch` | `Dispatch<A>` | 一个函数，你可以使用 `action` 调用它来更新 state。 |

### 示例
一个可以使用 reducer 进行递增或递减的计数器。

```javascript
import { useReducer } from 'react';

const initialState = { count: 0 };

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 };
    case 'decrement':
      return { count: state.count - 1 };
    default:
      throw new Error('Unsupported action type');
  }
}

function ReducerCounter() {
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <div>
      <p>Count: {state.count}</p>
      <button onClick={() => dispatch({ type: 'decrement' })}>-</button>
      <button onClick={() => dispatch({ type: 'increment' })}>+</button>
    </div>
  );
}
```
在这里，所有 state 更新逻辑都集中在 `reducer` 函数中。组件使用一个 action 对象调用 `dispatch`，以表示其更改 state 的意图。

## useOptimistic

`useOptimistic` 是一个 Hook，它允许你在异步 action 正在进行时显示一个不同的 state。它接受当前 state 并返回它的一个副本，这个副本在 action 持续期间可以有所不同。

### 签名
`useOptimistic<S, A>(passthrough: S, reducer: ?(S, A) => S): [S, (A) => void]`

### 参数

| Parameter | Type | Description |
| --- | --- | --- |
| `passthrough` | `S` | 当没有待处理的 action 时，默认将返回的 state 值。 |
| `reducer` | `?(S, A) => S` | *可选。* 一个函数，它接受当前 state 和 action 的有效负载，并返回新的乐观 state。如果未提供，则 action 的有效负载本身将成为乐观 state。 |

### 返回值
一个包含两个元素的数组：

| Index | Name | Type | Description |
| --- | --- | --- | --- |
| 0 | `optimisticState` | `S` | 乐观 state。除非有待处理的 action，否则它将等于 `passthrough`，在这种情况下，它由 `reducer` 计算得出。 |
| 1 | `addOptimistic` | `(A) => void` | 一个 dispatch 函数，用于在你想触发乐观更新时调用。它接受一个类型为 `A` 的 action 有效负载。 |

### 示例
一个消息列表示例，它在向服务器发送新消息时会乐观地添加该消息。

```javascript
import { useOptimistic, useState, useRef } from 'react';

async function sendMessage(message) {
  // Simulate a network delay
  await new Promise(res => setTimeout(res, 1000));
  return message;
}

function MessageList() {
  const formRef = useRef(null);
  const [messages, setMessages] = useState([]);
  const [optimisticMessages, addOptimisticMessage] = useOptimistic(
    messages,
    (currentMessages, newMessage) => [
      ...currentMessages,
      { text: newMessage, sending: true },
    ]
  );

  const formAction = async (formData) => {
    const message = formData.get('message');
    addOptimisticMessage(message);
    formRef.current.reset();
    await sendMessage(message);
    setMessages(prev => [...prev, { text: message }]);
  };

  return (
    <div>
      {optimisticMessages.map((msg, index) => (
        <div key={index}>
          {msg.text}
          {msg.sending && <small> (Sending...)</small>}
        </div>
      ))}
      <form action={formAction} ref={formRef}>
        <input type="text" name="message" placeholder="Your message" />
        <button type="submit">Send</button>
      </form>
    </div>
  );
}
```
当用户提交表单时，会调用 `addOptimisticMessage`，立即在 UI 中添加一条带有“Sending...”标签的新消息。一旦 `sendMessage` 函数完成，实际的 state 会通过 `setMessages` 进行更新，乐观的 UI 会与最终的 state 合并。

## useActionState

`useActionState` 是一个 Hook，它允许你根据表单 action 的结果更新 state。它对于处理表单提交、管理 pending 状态和服务器 action 的响应非常有用。

### 签名
`useActionState<S, P>(action: (S, P) => S, initialState: S, permalink?: string): [S, (P) => void, boolean]`

### 返回值
一个包含三个元素的数组：

| Index | Name | Type | Description |
| --- | --- | --- | --- |
| 0 | `state` | `S` | 当前 state。在初始渲染时，它是 `initialState`。在 action 运行后，它是 action 的返回值。 |
| 1 | `formAction` | `(P) => void` | 一个新的 action，你可以将其传递给你 `<form>` 组件的 `action` 属性。 |
| 2 | `isPending` | `boolean` | 一个布尔值，指示表单 action 当前是否处于 pending 状态（即 action 函数正在执行中）。 |

### 示例
一个使用 `useActionState` 管理其提交状态并显示响应的表单。

```javascript
import { useActionState } from 'react';

async function addToCart(previousState, formData) {
  const itemId = formData.get('itemId');
  // Simulate an API call
  await new Promise(resolve => setTimeout(resolve, 500));

  if (itemId === '123') {
    return { success: true, message: 'Item added to cart!' };
  }
  return { success: false, message: 'Failed to add item. Please try again.' };
}

function AddToCartForm() {
  const [state, formAction, isPending] = useActionState(addToCart, { success: null, message: '' });

  return (
    <form action={formAction}>
      <input type="hidden" name="itemId" value="123" />
      <button type="submit" disabled={isPending}>
        {isPending ? 'Adding...' : 'Add to Cart'}
      </button>
      {state.message && <p>{state.message}</p>}
    </form>
  );
}
```
此示例展示了一个将商品提交到购物车的表单。`isPending` 状态用于在提交期间禁用按钮，而 `state` 对象则保存来自 `addToCart` action 的响应，然后将其显示给用户。

---

既然你已经学会了如何管理组件 state，下一步是了解如何处理数据获取或订阅等副作用。请继续阅读 [Effect Hook](./hooks-effect.md) 文档以了解更多信息。
