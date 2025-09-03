# Hooks

Hooks 是一些函数，可以让你在函数组件中“钩入”React 的 state 及生命周期等特性。通过它们，你无需编写 class 即可使用 state、effect、context 以及其他 React 功能，从而让代码更易于阅读和组合。

在使用 Hooks 之前，你应该熟悉两条基本规则：
1.  **只在顶层调用 Hooks：** 不要在循环、条件判断或嵌套函数中调用 Hooks。
2.  **只从 React 函数中调用 Hooks：** 只能在 React 函数组件或自定义 Hook 中调用它们，而不能在普通的 JavaScript 函数中调用。

更多详情，请参阅官方文档中的 [Hooks 规则](https://react.dev/link/invalid-hook-call)。

本节按用途分组，详细介绍了所有内置的 Hooks。

```d2
direction: down

"React Hooks": {
  shape: hexagon
  
  "类别": {
    grid-columns: 3
    grid-gap: 50

    "State": {
      shape: package
      "useState"
      "useReducer"
      "useActionState"
      "useOptimistic"
    }

    "Effect": {
      shape: package
      "useEffect"
      "useLayoutEffect"
      "useInsertionEffect"
    }

    "性能": {
      shape: package
      "useMemo"
      "useCallback"
      "useTransition"
    }
    
    "Ref": {
      shape: package
      "useRef"
      "useImperativeHandle"
    }

    "Context": {
      shape: package
      "useContext"
    }

    "其他": {
      shape: package
      "useId"
      "useSyncExternalStore"
      "useDebugValue"
    }
  }

  "React Hooks" -> "类别": "按用途分组"
}
```

## Hook 类别

探索不同类型的 Hooks，以处理组件逻辑的各个方面。

<x-cards data-columns="2">
  <x-card data-title="State Hook" data-href="/hooks/state" data-icon="lucide:database">
    通过 `useState`、`useReducer`、`useActionState` 和 `useOptimistic` 管理组件状态。
  </x-card>
  <x-card data-title="Effect Hook" data-href="/hooks/effect" data-icon="lucide:zap">
    通过 `useEffect`、`useLayoutEffect` 和 `useInsertionEffect` 在组件中执行副作用。
  </x-card>
  <x-card data-title="Ref Hook" data-href="/hooks/ref" data-icon="lucide:git-commit">
    通过 `useRef` 和 `useImperativeHandle` 引用无需用于渲染的值。
  </x-card>
  <x-card data-title="性能 Hook" data-href="/hooks/performance" data-icon="lucide:gauge">
    通过 `useCallback`、`useMemo`、`useTransition` 和 `useDeferredValue` 优化组件性能。
  </x-card>
  <x-card data-title="其他 Hook" data-href="/hooks/other" data-icon="lucide:puzzle">
    探索 `useContext`、`useId`、`useDebugValue` 和 `useSyncExternalStore` 等其他 Hooks。
  </x-card>
</x-cards>

## 完整的 API 参考

以下是 React 中所有可用的内置 Hooks 的完整列表。

| Hook | Description |
|---|---|
| [`useState`](./hooks-state.md) | 在函数组件中管理 state。 |
| [`useEffect`](./hooks-effect.md) | 在渲染后执行副作用。 |
| [`useContext`](./hooks-other.md) | 订阅 React context，而无需引入嵌套。 |
| [`useReducer`](./hooks-state.md) | `useState` 的替代方案，用于管理复杂的状态逻辑。 |
| [`useCallback`](./hooks-performance.md) | 返回一个 memoized 回调函数。 |
| [`useMemo`](./hooks-performance.md) | 返回一个 memoized 值。 |
| [`useRef`](./hooks-ref.md) | 返回一个可变的 ref 对象。 |
| [`useImperativeHandle`](./hooks-ref.md) | 在使用 `ref` 时，自定义暴露给父组件的实例值。 |
| [`useLayoutEffect`](./hooks-effect.md) | 在所有 DOM 变更后同步触发。 |
| [`useInsertionEffect`](./hooks-effect.md) | 在所有 DOM 变更前同步触发，适用于 CSS-in-JS 库。 |
| [`useTransition`](./hooks-performance.md) | 让你能够在不阻塞 UI 的情况下更新 state。 |
| [`useDeferredValue`](./hooks-performance.md) | 推迟更新部分 UI。 |
| [`useId`](./hooks-other.md) | 生成在服务端和客户端之间保持稳定的唯一 ID。 |
| [`useSyncExternalStore`](./hooks-other.md) | 让你能够订阅外部 store。 |
| [`useDebugValue`](./hooks-other.md) | 在 React DevTools 中为自定义 Hook 显示标签。 |
| [`useActionState`](./hooks-state.md) | 管理表单 action 的状态。 |
| [`useOptimistic`](./hooks-state.md) | 管理乐观 UI 更新。 |
| `use` | 让你能够读取 Promise 或 context 等资源的值。 |

---

Hooks 是构建现代化、函数式 React 应用程序的基础。通过熟练掌握它们，你可以创建出高效、可读且易于维护的组件。

要了解这些 Hooks 如何在更复杂的模式和环境中应用，请继续阅读[高级指南](./advanced.md)。

