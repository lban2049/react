# Hooks

Hooks 是一些函数，可让你在函数组件中使用 state 和其他 React 功能。它们允许你从组件中“钩入”React 的 state 和生命周期功能，而无需编写类，使你的代码更具可重用性且更易于理解。

在使用 Hooks 之前，了解它们的两个主要规则至关重要：
1.  **只在顶层调用 Hooks：** 不要在循环、条件或嵌套函数中调用 Hooks。
2.  **只在 React 函数中调用 Hooks：** 从 React 函数组件中调用它们，而不是从常规的 JavaScript 函数中调用。

如需深入了解这些规则，请参阅关于 [Hooks 规则](https://react.dev/link/invalid-hook-call) 的官方文档。

本页按用途对内置 Hooks 进行了分类概述。选择一个类别以了解其中的具体 Hooks。

<x-cards data-columns="2">
  <x-card data-title="State Hooks" data-href="/hooks/state" data-icon="lucide:database">
    使用 `useState`、`useReducer`、`useActionState` 和 `useOptimistic` 管理组件的 state。
  </x-card>
  <x-card data-title="Effect Hooks" data-href="/hooks/effect" data-icon="lucide:zap">
    使用 `useEffect`、`useLayoutEffect` 和 `useInsertionEffect` 在组件中执行副作用。
  </x-card>
  <x-card data-title="Ref Hooks" data-href="/hooks/ref" data-icon="lucide:anchor">
    使用 `useRef` 和 `useImperativeHandle` 引用不需要用于渲染的值。
  </x-card>
  <x-card data-title="Performance Hooks" data-href="/hooks/performance" data-icon="lucide:gauge-circle">
    使用 `useCallback`、`useMemo`、`useTransition` 和 `useDeferredValue` 优化组件的性能。
  </x-card>
  <x-card data-title="Other Hooks" data-href="/hooks/other" data-icon="lucide:puzzle">
    探索其他 Hooks，如 `useContext`、`useId`、`useDebugValue` 和 `useSyncExternalStore`。
  </x-card>
</x-cards>

## API 快速参考

以下是 React 中所有内置 Hooks 的完整列表，以供快速参考。

| Hook | 描述 |
| --- | --- |
| `useState` | 声明一个可以直接更新的 state 变量。 |
| `useReducer` | 声明一个 state 变量，其复杂的更新逻辑由 reducer 函数管理。 |
| `useActionState` | 管理表单操作的状态，处理 pending 和 error 状态。 |
| `useOptimistic` | 管理可以回滚的乐观 UI 更新。 |
| `useEffect` | 将组件连接到外部系统，并在渲染后执行副作用。 |
| `useLayoutEffect` | 在所有 DOM 变更后同步触发，可用于测量布局。 |
| `useInsertionEffect` | 在 DOM 变更前同步触发，主要用于 CSS-in-JS 库。 |
| `useRef` | 引用一个不需要用于渲染的值，通常用于访问 DOM 元素。 |
| `useImperativeHandle` | 自定义暴露给父组件的 ref 句柄。 |
| `useCallback` | 在重新渲染之间缓存函数定义以优化性能。 |
| `useMemo` | 在重新渲染之间缓存计算结果。 |
| `useTransition` | 将 state 更新标记为非阻塞过渡，以避免 UI 卡顿。 |
| `useDeferredValue` | 延迟更新 UI 的非关键部分。 |
| `useContext` | 读取并订阅 context，允许数据沿组件树向下传递。 |
| `useId` | 生成一个在服务端和客户端渲染中保持稳定的唯一 ID。 |
| `useDebugValue` | 在 React DevTools 中为自定义 Hook 显示标签。 |
| `useSyncExternalStore` | 将组件订阅到外部数据存储。 |
| `use` | 读取资源（如 Promise 或 context）的值。 |


本概述提供了 React Hooks 世界的导览图。每个类别都包含了用于构建现代 React 应用的强大工具。我们建议从 [State Hooks](./hooks-state.md) 指南开始，因为它们是组件交互性的基础。