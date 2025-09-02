# Hooks

Hooks 是一些函数，可以让你在函数组件中“钩入”React 的 state 和生命周期功能。通过它们，你无需编写 class 即可使用 state、副作用、context 以及其他 React 功能。这使得你的组件逻辑更具可重用性、可组合性，也更易于测试。

在使用 Hooks 之前，了解其规则非常重要。最关键的一条是**只能在函数组件或自定义 Hook 的顶层调用 Hooks**。这确保了在每次组件渲染时都以相同的顺序调用 Hooks，React 正是以此来在多次调用之间保持 Hooks 的状态。更多详情，请参阅关于 [Hooks 规则](https://react.dev/link/invalid-hook-call) 的指南。

React 提供了多种内置 Hooks。为了帮助你理解它们的用途，我们将其分为以下几类：

<x-cards data-columns="2">
  <x-card data-title="State Hooks" data-href="/hooks/state" data-icon="lucide:database">
    管理组件的状态，从简单值到复杂逻辑和乐观 UI 更新。包括 `useState`、`useReducer`、`useActionState` 和 `useOptimistic`。
  </x-card>
  <x-card data-title="Effect Hooks" data-href="/hooks/effect" data-icon="lucide:zap">
    执行副作用，例如获取数据、订阅事件或手动操作 DOM。包括 `useEffect`、`useLayoutEffect` 和 `useInsertionEffect`。
  </x-card>
  <x-card data-title="Ref Hooks" data-href="/hooks/ref" data-icon="lucide:anchor">
    访问 DOM 节点或在多次渲染之间持久化值，而不会导致重新渲染。包括 `useRef` 和 `useImperativeHandle`。
  </x-card>
  <x-card data-title="性能 Hooks" data-href="/hooks/performance" data-icon="lucide:rocket">
    通过记忆化值、跳过昂贵的重新渲染以及管理非紧急的 UI 更新来优化应用程序的性能。包括 `useCallback`、`useMemo`、`useTransition` 和 `useDeferredValue`。
  </x-card>
  <x-card data-title="其他 Hooks" data-href="/hooks/other" data-icon="lucide:boxes">
    一组用于其他特定用例的 Hooks，例如访问 context、生成唯一 ID 以及订阅外部存储。包括 `useContext`、`useId` 等。
  </x-card>
</x-cards>

## Hooks 完整列表

以下是 React 中所有内置 Hooks 的完整列表，以供快速参考。

| Hook | Description |
|---|---|
| `useState` | 声明一个可以直接更新的状态变量。 |
| `useReducer` | 使用 reducer 函数管理复杂的状态逻辑，类似于 Redux。 |
| `useContext` | 读取并订阅一个 context，从而可以将数据深入传递到组件树中。 |
| `useEffect` | 在组件渲染后执行副作用，例如数据获取或订阅。 |
| `useLayoutEffect` | 在所有 DOM 变更后同步触发。可用于从 DOM 中读取布局。 |
| `useInsertionEffect` | 允许 CSS-in-JS 库在 `useLayoutEffect` 之前注入样式，以避免性能问题。 |
| `useCallback` | 返回一个记忆化版本的 callback 函数，该函数仅在某个依赖项发生变化时才会更改。 |
| `useMemo` | 返回一个记忆化值，仅在某个依赖项发生变化时才重新计算它。 |
| `useRef` | 返回一个可变的 ref 对象，其 `.current` 属性可以在多次渲染之间保持一个值。 |
| `useImperativeHandle` | 在使用 `ref` 时，自定义暴露给父组件的实例值。 |
| `useDebugValue` | 在 React DevTools 中为自定义 Hooks 显示一个标签。 |
| `useTransition` | 让你在不阻塞 UI 的情况下更新 state，将更新标记为非紧急。 |
| `useDeferredValue` | 推迟更新 UI 的一部分，有助于在大量计算期间保持界面响应。 |
| `useId` | 生成在服务端和客户端之间保持稳定的唯一 ID，以避免 hydration 不匹配问题。 |
| `useSyncExternalStore` | 订阅外部存储，确保组件在更新时重新渲染。 |
| `use` | 读取 Promise 或 context 等资源的值。 |
| `useActionState` | 管理表单 action 的状态，包括 pending 和响应状态。 |
| `useOptimistic` | 管理乐观 UI 更新，在异步 action 完成期间显示一个临时状态。 |

Hooks 是现代 React 开发的核心部分。了解如何以及何时使用每一种 Hook 是构建高效且可维护应用程序的关键。

首先，请探索最基础的 Hooks 类别。

### 后续步骤

继续下一节，学习 [State Hooks](./hooks-state.md)。