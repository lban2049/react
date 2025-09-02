# API 参考

本页为从 `react` 包导出的所有公共 API 提供了一个全面的、从 A 到 Z 的参考。它专为快速查找函数签名、参数和返回值而设计。

有关更详细的解释、用法模式和实践示例，请参阅主要的文档部分，例如[核心 API](./core-apis.md) 和 [Hooks](./hooks.md)。

## 组件和片段

这些是在 React 中创建用户界面的核心构建块。它们包括用于构建 UI 的基类和特殊组件。

| API | Description |
|---|---|
| `Component` | 用于定义使用 ES6 类的 React 组件的基类。 |
| `PureComponent` | 与 `Component` 类似，但它对 props 和 state 进行浅层比较，以防止不必要的重新渲染。 |
| `Fragment` | 允许你对一组子元素进行分组，而无需向 DOM 添加额外的节点。可以写作 `<>...</>`。 |
| `Profiler` | 一个用于测量 React 树渲染性能以帮助识别瓶颈的组件。 |
| `StrictMode` | 一个用于突出显示应用程序中潜在问题的工具。它会为其后代组件激活额外的检查和警告。 |
| `Suspense` | 允许你在其子组件异步加载时指定一个加载指示器 (fallback)。 |

## Hooks

Hooks 允许你在函数组件中使用 state 和其他 React 功能。它们必须在组件的顶层调用。

要获取完整指南，请参阅 [Hooks](./hooks.md) 文档。

| API | Description | Related Docs |
|---|---|---|
| `useState` | 为函数组件添加 state。 | [State Hooks](./hooks-state.md) |
| `useEffect` | 在函数组件中执行副作用（例如，数据获取、订阅）。 | [Effect Hooks](./hooks-effect.md) |
| `useContext` | 接受一个 context 对象，并返回该 context 的当前值。 | [Other Hooks](./hooks-other.md) |
| `useReducer` | `useState` 的替代方案，用于管理更复杂的状态逻辑。 | [State Hooks](./hooks-state.md) |
| `useCallback` | 返回一个 memoized 版本的 callback 函数，以防止不必要的重新渲染。 | [Performance Hooks](./hooks-performance.md) |
| `useMemo` | 返回一个 memoized 值，仅在其中一个依赖项发生变化时才重新计算它。 | [Performance Hooks](./hooks-performance.md) |
| `useRef` | 返回一个可变的 ref 对象，其值可以在组件的多次渲染之间保持不变。 | [Ref Hooks](./hooks-ref.md) |
| `useImperativeHandle` | 在使用 `ref` 时，自定义暴露给父组件的实例值。 | [Ref Hooks](./hooks-ref.md) |
| `useLayoutEffect` | 与 `useEffect` 类似，但在所有 DOM 突变后同步触发。 | [Effect Hooks](./hooks-effect.md) |
| `useInsertionEffect` | 在 DOM 突变被绘制到屏幕前同步触发，主要用于 CSS-in-JS 库。 | [Effect Hooks](./hooks-effect.md) |
| `useDebugValue` | 在 React DevTools 中为自定义 Hook 显示标签。 | [Other Hooks](./hooks-other.md) |
| `useDeferredValue` | 推迟更新 UI 的非关键部分。 | [Performance Hooks](./hooks-performance.md) |
| `useTransition` | 管理状态更新而不会阻塞 UI，有助于在慢速渲染期间保持 UI 的响应性。 | [Performance Hooks](./hooks-performance.md) |
| `useId` | 生成在服务器和客户端上都稳定的唯一 ID，以防止水合不匹配问题。 | [Other Hooks](./hooks-other.md) |
| `useSyncExternalStore` | 一个用于订阅外部数据源（例如，第三方状态管理器）的 Hook。 | [Other Hooks](./hooks-other.md) |
| `useOptimistic` | 管理乐观 UI 更新，如果底层的异步操作失败，这些更新将被回滚。 | [State Hooks](./hooks-state.md) |
| `useActionState` | 管理表单操作的待处理状态和返回值。 | [State Hooks](./hooks-state.md) |
| `use` | 一个用于读取资源（例如 Promise 或 context）值的 Hook。 | [Advanced Guides](./advanced.md) |

## 工具函数

这些函数可帮助你处理 React 元素、context 和 children。

| API | Description | Related Docs |
|---|---|---|
| `Children` | 提供用于处理 `props.children` 数据结构的工具函数（`map`、`forEach`、`count` 等）。 | [Children Utilities](./core-apis-children-utilities.md) |
| `cloneElement` | 以现有元素为起点，克隆并返回一个新的 React 元素。 | [Creating & Manipulating Elements](./core-apis-creating-elements.md) |
| `createContext` | 创建一个 Context 对象，用于在组件树中传递数据，而无需逐层传递 props。 | [Context](./core-apis-context.md) |
| `createElement` | 创建并返回一个新的 React 元素。JSX 会编译成对此函数的调用。 | [Creating & Manipulating Elements](./core-apis-creating-elements.md) |
| `createRef` | 创建一个 ref 对象，可以通过 `ref` 属性附加到元素上。 | [Refs](./core-apis-refs.md) |
| `forwardRef` | 创建一个可以将其接收到的 `ref` 转发给其子组件之一的组件。 | [Refs](./core-apis-refs.md) |
| `isValidElement` | 验证一个对象是否为有效的 React 元素。 | [Creating & Manipulating Elements](./core-apis-creating-elements.md) |
| `lazy` | 定义一个可以动态加载的组件（代码分割）。与 `Suspense` 一起使用。 | [Code Splitting](./advanced-code-splitting.md) |
| `memo` | 一个高阶组件，用于 memoize 一个组件，如果其 props 未更改，则可防止重新渲染。 | [Performance Hooks](./hooks-performance.md) |
| `startTransition` | 一个用于包装可能导致慢速渲染的状态更新的函数，以保持 UI 的响应性。 | [Transitions](./advanced-transitions.md) |

## 缓存 API

这些 API 主要用于缓存数据，尤其是在服务器环境中。

| API | Description | Related Docs |
|---|---|---|
| `cache` | 一个用于 memoize 数据获取或计算结果的函数。 | [Caching](./advanced-caching.md) |
| `unstable_getCacheForType` | （不稳定）为给定的资源类型检索缓存实例。 | [Caching](./advanced-caching.md) |
| `unstable_useCacheRefresh` | （不稳定）一个返回使 React 缓存失效的函数的 Hook。 | [Caching](./advanced-caching.md) |

## 实验性及不稳定 API

> **警告**
> 这些 API 是实验性的，可能会在未来的版本中更改或删除。请谨慎使用，且仅用于测试或评估目的。

| API | Description | Related Docs |
|---|---|---|
| `experimental_useEffectEvent` | （实验性）一个用于定义不会重新触发 effect 的事件处理程序的 Hook。 | [Experimental APIs](./advanced-experimental.md) |
| `unstable_Activity` | （不稳定）一个用于管理离屏 UI 状态的组件。 | [Experimental APIs](./advanced-experimental.md) |
| `unstable_postpone` | （不稳定）一个推迟组件树渲染的函数。 | [Experimental APIs](./advanced-experimental.md) |
| `unstable_SuspenseList` | （不稳定）一个用于协调多个 `Suspense` 边界加载顺序的组件。 | [Experimental APIs](./advanced-experimental.md) |
| `unstable_ViewTransition` | （不稳定）一个用于创建动画视图过渡的 Hook。 | [Experimental APIs](./advanced-experimental.md) |

## 其他导出项

提供版本信息或专用于特定环境的其他导出项。

| API | Description |
|---|---|
| `version` | 一个表示当前 React 版本的字符串（例如，“18.3.0”）。 |
| `act` | 一个测试工具，可确保在进行断言之前处理完更新。仅在开发和测试版本中可用。 |
