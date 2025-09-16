# Hooks

Hooks 是一些函数，可让你在函数组件中“钩入”React 的 state 及生命周期等特性。通过 Hooks，你无需编写 class 即可使用 state、effect、context 以及其他 React 功能。Hooks 是构建现代 React 应用的基础，它使得逻辑的复用和组合更加容易。

在使用 Hooks 之前，你应该熟悉两条主要规则：
1.  只在函数组件或自定义 Hooks 的顶层调用 Hooks。
2.  只在 React 函数组件中调用 Hooks，不要在普通 JavaScript 函数中调用。

更多详情，请参阅关于 [Hooks 规则](https://react.dev/link/invalid-hook-call) 的官方文档。

本节提供了 React 中所有内置 Hooks 的详细参考。

## State Hooks

State Hooks 允许组件管理局部状态。

### useState

返回一个带状态的值和一个更新它的函数。

**签名**
```javascript
const [state, setState] = useState(initialState);
```

**参数**

<x-field data-name="initialState" data-type="S | () => S" data-required="true" data-desc="用于初始化 state 的值。可以是任何类型的值，或一个返回初始 state 的函数。该函数仅在初始渲染时执行。"></x-field>

**返回**

一个包含两个元素的数组：

<x-field data-name="state" data-type="S" data-required="true" data-desc="当前 state。在首次渲染时，它将与 initialState 匹配。"></x-field>

<x-field data-name="setState" data-type="Dispatch<BasicStateAction<S>>" data-required="true" data-desc="state 的设置函数。它允许你将 state 更新为新值并触发重新渲染。可以用新 state 或一个接收前一个 state 并返回新 state 的函数来调用它。"></x-field>

**示例**

```javascript Counter Component icon=logos:javascript
import { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>你点击了 {count} 次</p>
      <button onClick={() => setCount(count + 1)}>
        点我
      </button>
    </div>
  );
}
```

### useReducer

`useState` 的替代方案。接受一个类型为 `(state, action) => newState` 的 reducer，并返回当前的 state 及配套的 `dispatch` 方法。

**签名**
```javascript
const [state, dispatch] = useReducer(reducer, initialArg, init?);
```

**参数**

<x-field data-name="reducer" data-type="(S, A) => S" data-required="true" data-desc="一个指定 state 如何更新的函数。它必须是纯函数，接收 state 和 action 作为参数，并返回下一个 state。"></x-field>

<x-field data-name="initialArg" data-type="I" data-required="true" data-desc="用于计算初始 state 的值。"></x-field>

<x-field data-name="init" data-type="I => S" data-required="false" data-desc="一个可选的初始化函数。如果提供了该函数，初始 state 将被设置为 init(initialArg)。"></x-field>

**返回**

一个包含两个元素的数组：

<x-field data-name="state" data-type="S" data-required="true" data-desc="当前 state。"></x-field>

<x-field data-name="dispatch" data-type="Dispatch<A>" data-required="true" data-desc="可用于向 reducer 派发 action 的 dispatch 函数。"></x-field>

**示例**

```javascript Counter with Reducer icon=logos:javascript
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
      计数: {state.count}
      <button onClick={() => dispatch({type: 'decrement'})}>-</button>
      <button onClick={() => dispatch({type: 'increment'})}>+</button>
    </>
  );
}
```

### useActionState

为服务器操作添加状态管理，提供待定和响应状态。

**签名**
```javascript
const [state, formAction, isPending] = useActionState(action, initialState, permalink?);
```

**参数**

<x-field data-name="action" data-type="(Awaited<S>, P) => S" data-required="true" data-desc="当表单提交或按钮点击时执行的函数。"></x-field>

<x-field data-name="initialState" data-type="Awaited<S>" data-required="true" data-desc="初始 state 值。"></x-field>

<x-field data-name="permalink" data-type="string" data-required="false" data-desc="一个可选的 URL，操作完成后将重定向到该 URL。"></x-field>

**返回**

一个包含三个元素的数组：

<x-field data-name="state" data-type="Awaited<S>" data-required="true" data-desc="当前 state。最初是 initialState，在 action 调用后，是 action 的返回值。"></x-field>

<x-field data-name="formAction" data-type="(P) => void" data-required="true" data-desc="传递给 <form> 组件的 action。"></x-field>

<x-field data-name="isPending" data-type="boolean" data-required="true" data-desc="一个布尔值，指示 action 当前是否处于待定状态。在 action 执行期间为 true。"></x-field>

### useOptimistic

允许你在后台操作（如网络请求）完成之前，乐观地更新 UI。

**签名**
```javascript
const [optimisticState, addOptimistic] = useOptimistic(passthrough, reducer?);
```

**参数**

<x-field data-name="passthrough" data-type="S" data-required="true" data-desc="当没有乐观更新正在进行时将返回的 state。"></x-field>

<x-field data-name="reducer" data-type="(S, A) => S" data-required="false" data-desc="一个 reducer 函数，接收当前 state 和乐观值，并返回新的乐观 state。"></x-field>

**返回**

一个包含两个元素的数组：

<x-field data-name="optimisticState" data-type="S" data-required="true" data-desc="乐观 state 值。除非有更新正在进行，否则它将等于 `passthrough` state。"></x-field>

<x-field data-name="addOptimistic" data-type="(A) => void" data-required="true" data-desc="一个函数，用于调用你想要乐观应用的值。"></x-field>

## Effect Hooks

Effect Hooks 允许你在函数组件中执行副作用操作，例如数据获取、订阅或手动更改 DOM。

### useEffect

接受一个包含命令式、可能有副作用代码的函数。该函数在渲染提交到屏幕后运行。

**签名**
```javascript
useEffect(create, deps?);
```

**参数**

<x-field data-name="create" data-type="() => (() => void) | void" data-required="true" data-desc="一个作为副作用运行的函数。它可以选择性地返回一个清理函数，React 将在组件卸载或因依赖项更改而重新运行 effect 之前运行该函数。"></x-field>

<x-field data-name="deps" data-type="Array<mixed>" data-required="false" data-desc="一个可选的依赖项数组。只有当某个依赖项自上次渲染以来发生变化时，effect 才会重新运行。如果省略，effect 会在每次渲染后运行。"></x-field>

**示例**

```javascript Document Title Updater icon=logos:javascript
import { useState, useEffect } from 'react';

function TitleUpdater() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `你点击了 ${count} 次`;
  }, [count]); // 仅在 count 更改时重新运行 effect

  return (
    <div>
      <p>检查文档标题！</p>
      <button onClick={() => setCount(count + 1)}>
        点我
      </button>
    </div>
  );
}
```

### useLayoutEffect

其签名与 `useEffect` 相同，但在所有 DOM 变更后同步触发。使用它来从 DOM 读取布局并同步重新渲染。

**签名**
```javascript
useLayoutEffect(create, deps?);
```

### useInsertionEffect

其签名与 `useEffect` 相同，但在任何 DOM 变更前同步触发。这是为 CSS-in-JS 库的作者注入样式而设计的。应谨慎使用。

**签名**
```javascript
useInsertionEffect(create, deps?);
```

## Ref Hooks

Ref Hooks 提供了一种访问 DOM 节点或在多次渲染之间持久化可变值的方法。

### useRef

返回一个可变的 ref 对象，其 `.current` 属性被初始化为传入的参数（`initialValue`）。返回的对象在组件的整个生命周期内保持不变。

**签名**
```javascript
const refContainer = useRef(initialValue);
```

**参数**

<x-field data-name="initialValue" data-type="T" data-required="true" data-desc="ref 的 `current` 属性的初始值。"></x-field>

**返回**

<x-field data-name="refObject" data-type="{ current: T }" data-required="true" data-desc="一个带有单个可变 `current` 属性的 ref 对象。"></x-field>

**示例**

```javascript Focus Input icon=logos:javascript
import { useRef, useEffect } from 'react';

function TextInputWithFocusButton() {
  const inputEl = useRef(null);
  const onButtonClick = () => {
    // `current` 指向已挂载的文本输入元素
    inputEl.current.focus();
  };

  return (
    <>
      <input ref={inputEl} type="text" />
      <button onClick={onButtonClick}>聚焦输入框</button>
    </>
  );
}
```

### useImperativeHandle

在使用 `ref` 时，自定义暴露给父组件的实例值。与往常一样，在大多数情况下应避免使用 refs 的命令式代码。

**签名**
```javascript
useImperativeHandle(ref, create, deps?);
```

**参数**

<x-field data-name="ref" data-type="React.Ref<T>" data-required="true" data-desc="从父组件传递下来的 ref，通常来自 `forwardRef`。"></x-field>

<x-field data-name="create" data-type="() => T" data-required="true" data-desc="一个返回 ref 应暴露的值的函数。该值可以是一个带有自定义方法的对象。"></x-field>

<x-field data-name="deps" data-type="Array<mixed>" data-required="false" data-desc="一个可选的依赖项数组。只有当某个依赖项发生变化时，`create` 函数才会被重新调用。"></x-field>

## 其他 Hooks

本节涵盖其余的内置 Hooks。

| Hook | Description |
|---|---|
| `useContext` | 接受一个 context 对象并返回当前的 context 值。 |
| `useMemo` | 返回一个 memoized 值，仅在依赖项改变时才重新计算。 |
| `useCallback` | 返回一个 memoized 回调函数，仅在依赖项改变时才创建新函数。 |
| `useDebugValue` | 在 React DevTools 中为自定义 hook 显示标签。 |
| `useTransition` | 返回一个表示过渡待定状态的值，以及一个启动过渡的函数。 |
| `useDeferredValue` | 接受一个值并返回该值的新副本，该副本会延迟以响应更紧急的更新。 |
| `useId` | 生成在服务器和客户端之间保持稳定的唯一 ID。 |
| `useSyncExternalStore` | 推荐用于以兼容并发渲染特性的方式从外部数据源读取和订阅数据。 |

对于更高级的用例和实验性功能，你可以探索像 `use` 这样的 API 来读取 promise 或 context 的值。请记得查阅 [高级指南](./advanced-guides.md) 以了解前沿功能。

掌握 Hooks 后，你可能想了解更多关于 [顶层 API](./api-reference-top-level.md) 的知识，例如 `memo` 和 `lazy`，它们可以帮助你进一步优化组件。
