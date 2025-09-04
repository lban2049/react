# Hook

Hook 是一些函数，可让你在函数组件中“钩入” React 的 state 和生命周期等功能。它们允许你在不编写 class 的情况下使用 state 和其他 React 功能，使你的组件更简洁、更易于复用。

Hook 只能在函数组件的顶层或自定义 Hook 中调用。你不能在循环、条件或嵌套函数中调用它们。这确保了每次组件渲染时，Hook 的调用顺序都是相同的，从而让 React 能够在多次 `useState` 和 `useEffect` 调用之间正确地保留 Hook 的状态。

### Hook 的工作原理

从高层次来看，Hook 为你已知的 React 功能（如 state、lifecycle、context 和 refs）提供了直接的 API。当你调用像 `useState` 这样的 Hook 时，你就是在告诉 React，你的组件需要跟踪某些 state。

```d2
direction: down

"函数组件": {
  shape: rectangle
  style.fill: "#e6f7ff"

  "Hooks": {
    label: "Hook 调用\n(useState, useEffect, ...)"
    shape: rectangle
    style.fill: "#f6ffed"
  }
}

"React 核心": {
  shape: package
  label: "React 核心功能"
  style.fill: "#fff7e6"

  State: { shape: stored_data }
  Lifecycle: { shape: step }
  Context: { shape: stored_data }
}

"函数组件".Hooks -> "React 核心": "钩入"
"React 核心" -> "函数组件": "提供 State 和生命周期"
```

### 探索 Hook

React 提供了一系列内置 Hook 来涵盖广泛的使用场景。下面根据其主要用途对它们进行了分组。你可以深入每个部分以了解更多信息。

<x-cards data-columns="2">
  <x-card data-title="State Hook" data-icon="lucide:database" data-href="/hooks/state">
    管理组件的本地 state。这些 Hook 可以让你的组件记住诸如用户输入、服务器响应或 UI state 之类的信息。
  </x-card>
  <x-card data-title="Effect Hook" data-icon="lucide:zap" data-href="/hooks/effect">
    在组件中执行副作用。Effect 用于数据获取、设置订阅或手动更改 DOM。
  </x-card>
  <x-card data-title="Ref Hook" data-icon="lucide:anchor" data-href="/hooks/ref">
    引用无需用于渲染的值。可用于直接访问 DOM 节点或保留一个可变值。
  </x-card>
  <x-card data-title="Performance Hook" data-icon="lucide:gauge-circle" data-href="/hooks/performance">
    通过跳过昂贵的重新计算和在不阻塞用户的情况下管理 UI 更新来优化组件性能。
  </x-card>
  <x-card data-title="其他 Hook" data-icon="lucide:puzzle" data-href="/hooks/other">
    一系列用于其他特定使用场景的 Hook，例如读取 context、生成唯一 ID 或订阅外部 store。
  </x-card>
</x-cards>

### 完整的 API 参考

为了方便快速查找，这里列出了 React 中所有可用的内置 Hook。

| Hook | 描述 |
| --- | --- |
| `useState` | 声明一个 state 变量，你可以更新它，从而触发重新渲染。 |
| `useReducer` | `useState` 的替代方案，用于管理复杂 state 逻辑。 |
| `useEffect` | 让你在函数组件中执行副作用。 |
| `useLayoutEffect` | 在所有 DOM 变更后同步触发。使用它来从 DOM 读取布局并同步重新渲染。 |
| `useInsertionEffect` | 允许在任何布局 effect 触发前将元素插入到 DOM 中。主要用于 CSS-in-JS 库。 |
| `useContext` | 接收一个 context 对象并返回该 context 的当前值。 |
| `useRef` | 返回一个可变的 ref 对象，其 `.current` 属性被初始化为传入的参数。 |
| `useCallback` | 返回一个 memoized 的回调函数。 |
| `useMemo` | 返回一个 memoized 的值。 |
| `useImperativeHandle` | 在使用 `ref` 时，自定义暴露给父组件的实例值。 |
| `useTransition` | 让你在不阻塞 UI 的情况下更新 state。返回一个表示 transition 挂起状态的 stateful 值，以及一个启动它的函数。 |
| `useDeferredValue` | 让你延迟更新部分 UI。 |
| `useId` | 一个用于生成在服务端和客户端之间保持稳定的唯一 ID 的 hook。 |
| `useSyncExternalStore` | 一个推荐用于从外部数据源读取和订阅的 hook，其方式与并发渲染功能兼容。 |
| `useDebugValue` | 可用于在 React DevTools 中为自定义 hook 显示标签。 |
| `useActionState` | 一个用于管理表单 action 状态的 hook。 |
| `useOptimistic` | 一个让你能够乐观地更新 UI 的 hook。 |
| `use` | 一个允许你读取 Promise 或 context 等资源值的 hook。 |

### 后续步骤

首先，一个很好的起点是学习如何在组件中添加和管理 state。这是创建交互式用户界面的基础。

下一步：[State Hook](./hooks-state.md)
