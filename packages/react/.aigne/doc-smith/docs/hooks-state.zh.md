# State Hook

State 是所有交互式 React 应用的核心。State Hook 提供了一种在函数组件中跨重新渲染保留和管理数据的方式。它们允许你的组件响应用户输入、网络响应以及任何其他随时间发生的变化。

本指南介绍了用于管理组件 state 的主要 Hook。如需了解 state 和 props 工作原理的基础知识，可以查阅 [组件和 Props](./core-apis-components-and-props.md)。

<x-cards data-columns="2">
  <x-card data-title="useState" data-icon="lucide:variable">
    最常用的 Hook，用于管理数字、字符串或布尔值等简单 state 值。
  </x-card>
  <x-card data-title="useReducer" data-icon="lucide:network">
    适用于管理具有多个子值的复杂 state 逻辑，或下一个 state 依赖于前一个 state 的情况。
  </x-card>
  <x-card data-title="useOptimistic" data-icon="lucide:fast-forward">
    通过立即反映 state 变化来增强用户体验，然后在底层异步操作失败时回滚。
  </x-card>
  <x-card data-title="useActionState" data-icon="lucide:edit">
    管理表单操作的 state，跟踪挂起状态和来自服务器的响应。
  </x-card>
</x-cards>


## useState

`useState` 是最基础的 State Hook。它声明一个“state 变量”，你可以直接更新它来触发重新渲染。

### Signature

```javascript
const [state, setState] = useState(initialState);
```

**Parameters**

| Name | Type | Description |
|---|---|---|
| `initialState` | `S` or `() => S` | state 的初始值。它可以是任何类型的值。它也可以是一个函数，该函数只会在初始渲染期间执行以计算初始 state。 |

**Returns**

一个包含两个元素的数组：
1.  **当前 state：** 当前渲染的 state 变量的值。
2.  **`setState` 函数：** 一个可以让你将 state 更新为新值并触发重新渲染的函数。

### 示例：一个简单的计数器

```javascript
import { useState } from 'react';

function Counter() {
  // 声明一个名为 "count" 的新 state 变量
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
在此示例中，`useState(0)` 将 `count` state 变量初始化为 `0`。当用户点击按钮时，会调用 `setCount(count + 1)`，这会更新 state 并导致组件使用新的 count 值重新渲染。

## useReducer

`useReducer` 是 `useState` 的替代方案，用于管理更复杂的 state 逻辑。当你有多个子值或下一个 state 依赖于前一个 state 时，它特别有用。它遵循 Redux 模式，使用 reducer 函数来管理 state 转换。

### Signature

```javascript
const [state, dispatch] = useReducer(reducer, initialArg, init?);
```

**Parameters**

| Name | Type | Description |
|---|---|---|
| `reducer` | `(S, A) => S` | 一个指定 state 如何更新的函数。它接收当前 state 和一个 action，并应返回下一个 state。 |
| `initialArg` | `I` | 用于计算初始 state 的值。 |
| `init` | `(I) => S` | （可选）一个返回初始 state 的初始化函数。如果未提供，则初始 state 设置为 `initialArg`。 |

**Returns**

一个包含两个元素的数组：
1.  **当前 state：** 当前的 state 值。
2.  **`dispatch` 函数：** 一个可以通过 action 来更新 state 的函数。

### 示例：管理复杂 State

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
在这里，所有的 state 更新逻辑都集中在 `reducer` 函数中。组件通过一个 action 对象调用 `dispatch` 来触发 state 更新，这使得组件的事件处理程序更清晰，state 逻辑更可预测。

## useOptimistic

`useOptimistic` 是一个 Hook，它允许你在异步操作进行期间向用户展示一个不同的 state。它接收当前 state 并提供一个可以立即更新的“乐观”版本。这个乐观 state 会一直使用，直到异步操作（例如网络请求）完成，此时 state 会更新为最终值。

### Signature

```javascript
const [optimisticState, addOptimistic] = useOptimistic(passthrough, reducer?);
```

**Parameters**

| Name | Type | Description |
|---|---|---|
| `passthrough` | `S` | 在没有乐观更新激活时将返回的值。这通常是你的实际 state。 |
| `reducer` | `(S, A) => S` | （可选）一个函数，它接收当前 state 和传递给 `addOptimistic` 的值，并返回新的乐观 state。 |

**Returns**

一个包含两个元素的数组：
1.  **`optimisticState`：** 乐观 state 值。除非有更新处于活动状态，否则它将等于 `passthrough`。
2.  **`addOptimistic`：** 一个函数，可以通过传入更新值来立即更改 `optimisticState`。

### 示例：乐观地添加消息

```javascript
import { useOptimistic, useState, useRef } from 'react';

async function deliverMessage(message) {
  // 模拟网络请求
  await new Promise(res => setTimeout(res, 1000));
  // 在此演示中，我们假设它总是失败以显示回滚行为
  throw new Error('Message could not be sent'); 
}

function Thread({ messages, setMessages }) {
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
      // 在这里，你会将消息发送到服务器
      await deliverMessage(message);
      // 成功时，更新真实 state
      setMessages(prev => [...prev, { text: message }]);
    } catch (e) {
      // 发生错误时，乐观更新会自动回滚
      console.error(e);
    }
  }

  return (
    <>
      {optimisticMessages.map((m, i) => (
        <div key={i}>{m.text}{m.sending && <small> (Sending...)</small>}</div>
      ))}
      <form action={formAction} ref={formRef}>
        <input type="text" name="message" />
        <button type="submit">Send</button>
      </form>
    </>
  );
}
```
当用户提交表单时，消息会立即出现在列表中，并带有一个“(Sending...)”标签。由于我们的 `deliverMessage` 函数失败了，React 会自动将 UI 恢复到最后一个已知的真实 state (`messages`)。

## useActionState

`useActionState` 是一个用于管理表单操作 state 的 Hook。它提供上一次表单提交的 state、要传递给 `<form>` 的 action 以及表单的挂起状态。

### Signature

```javascript
const [state, formAction, isPending] = useActionState(action, initialState, permalink?);
```

**Parameters**

| Name | Type | Description |
|---|---|---|
| `action` | `(S, P) => S` | 表单提交时要执行的函数。它接收前一个 state 和表单的有效负载。 |
| `initialState` | `S` | 初始 state 值。 |
| `permalink` | `string` | （可选）表单成功提交后要重定向到的 URL。 |

**Returns**

一个包含三个元素的数组：
1.  **`state`：** 当前 state。在首次渲染时，它与 `initialState` 匹配，之后则匹配上一个 action 的返回值。
2.  **`formAction`：** 要传递给 `<form>` 的 `action` prop 的 action。
3.  **`isPending`：** 一个布尔值，指示表单 action 当前是否处于挂起状态。

### 示例：处理表单提交状态

```javascript
import { useActionState } from 'react';

async function increment(previousState, formData) {
  // 模拟异步工作
  await new Promise(res => setTimeout(res, 500));
  return previousState + 1;
}

function StatefulForm() {
  const [count, formAction, isPending] = useActionState(increment, 0);

  return (
    <form action={formAction}>
      <p>Count: {count}</p>
      <button type="submit" disabled={isPending}>
        {isPending ? 'Incrementing...' : 'Increment'}
      </button>
    </form>
  );
}
```
此示例展示了一个表单，其中点击按钮会增加一个计数器。在异步 `increment` action 运行时，`isPending` 为 `true`，这允许 UI 禁用按钮并显示加载消息。

## 选择你的 State Hook

决定使用哪个 state hook 取决于你组件 state 的复杂性。

```d2
direction: down

State-Complexity: {
  label: "你的组件 state 有多复杂？"
  shape: diamond
}

Simple-State: {
  label: "简单 State\n(布尔值、字符串、数字)"
  shape: rectangle
}

Complex-State: {
  label: "复杂 State\n(对象、数组、多个相互依赖的值)"
  shape: rectangle
}

Use-useState: {
  label: "使用 useState"
  shape: oval
  tooltip: "最适合简单、独立的 state 值。"
}

Use-useReducer: {
  label: "使用 useReducer"
  shape: oval
  tooltip: "最适合可预测的 state 转换和复杂逻辑。"
}

Needs-Optimistic-UI: {
  label: "是否需要为异步操作提供乐观 UI？"
  shape: diamond
}

Use-useOptimistic: {
  label: "使用 useOptimistic"
  shape: oval
  tooltip: "改善有延迟操作的感知性能。"
}

Is-Form-Action: {
  label: "State 是否与表单操作绑定？"
  shape: diamond
}

Use-useActionState: {
  label: "使用 useActionState"
  shape: oval
  tooltip: "管理表单提价状态，包括挂起状态。"
}

State-Complexity -> Simple-State: "简单"
State-Complexity -> Complex-State: "复杂"

Simple-State -> Use-useState
Complex-State -> Use-useReducer

Use-useState -> Needs-Optimistic-UI
Use-useReducer -> Needs-Optimistic-UI

Needs-Optimistic-UI -> Use-useOptimistic: "是"
Needs-Optimistic-UI -> Is-Form-Action: "否"

Is-Form-Action -> Use-useActionState: "是"
```

*   **从 `useState` 开始：** 它非常适合简单、独立的 state 值。
*   **升级到 `useReducer`：** 当 state 逻辑变得复杂、涉及多个子值，或者当下一个 state 以一种不简单的方式依赖于前一个 state 时。
*   **添加 `useOptimistic`：** 当你需要为异步操作提供即时反馈以改善用户体验时。
*   **使用 `useActionState`：** 当专门管理与表单提交相关的 state 时，特别是需要跟踪挂起状态和处理服务器响应时。

---

现在你已经了解了如何管理 state，下一步是学习如何处理副作用，例如获取数据或订阅事件。请继续阅读 [Effect Hook](./hooks-effect.md) 指南以了解更多信息。
