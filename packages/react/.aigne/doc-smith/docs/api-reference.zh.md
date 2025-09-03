# API 参考

本节提供 React 中所有公共 API 的全面参考。旨在帮助你快速查找需要使用的任何 React 功能的详细信息。为了便于浏览，这些 API 按类别分组。

如需更具指导性的介绍，请参阅 [核心 API](./core-apis.md) 和 [Hooks](./hooks.md) 部分。

<x-cards data-columns="2">
  <x-card data-title="核心组件和 API" data-icon="lucide:box" data-href="#core-components-apis">
    组件、Fragment 和 Suspense 等基本构建块。
  </x-card>
  <x-card data-title="Hooks" data-icon="lucide:anchor" data-href="#hooks">
    让你能够接入 React 状态和生命周期功能的函数。
  </x-card>
  <x-card data-title="实用工具" data-icon="lucide:wrench" data-href="#utilities">
    用于处理元素、子元素等的辅助函数。
  </x-card>
  <x-card data-title="不稳定和实验性 API" data-icon="lucide:flask-conical" data-href="#unstable--experimental-apis">
    正在开发中且可能会发生变化的 API。
  </x-card>
</x-cards>


## 核心组件和 API

这些是你将用于定义和构建应用程序 UI 的主要导出。

| API | Description |
|---|---|
| `Component` | 使用 ES6 类时 React 组件的基类。 |
| `PureComponent` | 与 `Component` 类似，但通过浅层 prop 和 state 比较实现了 `shouldComponentUpdate()`。 |
| `Fragment` | 让你能够对一组子元素进行分组，而无需向 DOM 添加额外的节点。 |
| `Profiler` | 衡量 React 组件树的渲染性能。 |
| `StrictMode` | 一个用于突出显示应用程序中潜在问题的工具。它会为其后代激活额外的检查和警告。 |
| `Suspense` | 让你的组件在加载数据时可以显示一个备用 UI。请参阅 [代码分割](./advanced-code-splitting.md) 指南。 |
| `lazy` | 让你能够定义一个动态加载的组件。 |
| `memo` | 一个用于记忆化组件的高阶组件，如果其 props 未发生变化，则可防止重新渲染。 |

## Hooks

Hooks 允许你在函数组件中使用 state 和其他 React 功能。它们只能在函数组件的顶层或你自己的自定义 Hooks 中调用。有关详细用法，请参阅主要的 [Hooks 文档](./hooks.md)。

| Hook | Description |
|---|---|
| [`useState`](./hooks-state.md) | 为函数组件添加 state。 |
| [`useEffect`](./hooks-effect.md) | 让您可以在函数组件中执行副作用。 |
| [`useContext`](./hooks-other.md) | 接受一个 context 对象并返回当前的 context 值。 |
| [`useReducer`](./hooks-state.md) | `useState` 的替代方案，用于管理复杂的状态逻辑。 |
| [`useCallback`](./hooks-performance.md) | 返回一个记忆化的回调函数。 |
| [`useMemo`](./hooks-performance.md) | 返回一个记忆化的值。 |
| [`useRef`](./hooks-ref.md) | 返回一个可变 ref 对象。 |
| [`useImperativeHandle`](./hooks-ref.md) | 自定义在使用 `ref` 时向父组件公开的实例值。 |
| [`useLayoutEffect`](./hooks-effect.md) | 在所有 DOM 变更后同步触发。 |
| [`useInsertionEffect`](./hooks-effect.md) | 在所有 DOM 变更前同步触发，通常用于 CSS-in-JS 库。 |
| [`useDebugValue`](./hooks-other.md) | 可用于在 React DevTools 中为自定义 Hooks 显示标签。 |
| [`useTransition`](./hooks-performance.md) | 让你可以在不阻塞 UI 的情况下更新 state。 |
| [`useDeferredValue`](./hooks-performance.md) | 让你能够延迟更新 UI 的一部分。 |
| [`useId`](./hooks-other.md) | 生成在服务器和客户端之间保持稳定的唯一 ID。 |
| [`useSyncExternalStore`](./hooks-other.md) | 一个用于订阅外部存储的 Hook。 |
| [`useActionState`](./hooks-state.md) | 一个用于管理表单操作状态的 Hook。 |
| [`useOptimistic`](./hooks-state.md) | 一个让你可以乐观地更新 UI 的 Hook。 |
| `use` | 一个用于读取资源（如 Promise 或 context）值的 Hook。 |

## 实用工具

React 提供了一组实用函数，以帮助你处理元素、子元素和其他核心概念。

### 元素操作

| Utility | Description |
|---|---|
| `createElement` | 创建并返回一个给定类型的新 React 元素。 |
| `cloneElement` | 以一个元素为起点，克隆并返回一个新的 React 元素。 |
| `isValidElement` | 验证对象是否为 React 元素。 |

### Refs 和 Context

| Utility | Description |
|---|---|
| `createRef` | 创建一个可以附加到 React 元素的 ref 对象。 |
| `forwardRef` | 创建一个 React 组件，该组件将其接收到的 ref 转发给树中下层的另一个组件。 |
| `createContext` | 创建一个 Context 对象。请参阅 [Context](./core-apis-context.md) 指南。 |

### Children 实用工具

`React.Children` 对象提供了处理 `props.children` 数据结构的实用工具。

| Utility | Description |
|---|---|
| `Children.map` | 对 `children` 中包含的每个直接子元素调用一个函数。 |
| `Children.forEach` | 与 `Children.map()` 类似，但不返回数组。 |
| `Children.count` | 返回 `children` 中组件的总数。 |
| `Children.toArray` | 将 `children` 不透明数据结构作为扁平数组返回。 |
| `Children.only` | 验证 `children` 只有一个子元素并将其返回。 |

### 其他实用工具

| Utility | Description |
|---|---|
| `version` | 包含当前 React 版本的字符串。 |
| `act` | 一个用于测试组件的辅助函数，它以更接近 React 在浏览器中工作的方式包装渲染和更新。（仅限开发环境） |
| `cache` | 一个用于记忆化数据获取函数的实用工具。请参阅 [缓存](./advanced-caching.md)。 |

## 不稳定和实验性 API

> **警告**
> 这些 API 尚不稳定，可能会在未来的版本中被更改或删除。在生产应用程序中使用它们需要自担风险。

| API | Description |
|---|---|
| `unstable_SuspenseList` | 协调多个 Suspense 组件的加载顺序。 |
| `unstable_LegacyHidden` | 一个用于隐藏子树不被渲染的组件。 |
| `unstable_Activity` | 一个用于指示子树当前处于活动或可见状态的组件。 |
| `unstable_Scope` | 提供一种将事件监听器限定在特定子树范围内的方法。 |
| `unstable_TracingMarker` | 一个用于性能追踪的内部组件。 |
| `unstable_ViewTransition` | 一个用于为导航启用视图过渡的组件。 |
| `unstable_useCacheRefresh` | 一个用于使 React 缓存无效的 Hook。 |
| `unstable_getCacheForType` | 检索给定类型的缓存实例。 |
| `experimental_useEffectEvent` | 一个用于声明 Effect Event 的 Hook，Effect Event 是 Effect 内部的一段非响应式逻辑。 |