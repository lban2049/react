# API 参考

本页为 React 库中的所有公共 API 提供了全面的参考。内容按类别组织，以帮助你快速找到所需信息。如需更详细的解释和用法示例，请点击链接访问相关文档章节。

## Hooks

Hooks 允许你在函数组件中使用 state 和其他 React 功能。它们必须在组件的顶层调用。

有关详细指南，请参阅 [Hooks](./hooks.md) 文档。

| Hook | Description |
|---|---|
| [`use`](./hooks-other.md) | 读取 Promise 或 context 等资源的值。 |
| [`useActionState`](./hooks-state.md) | 管理表单操作的状态。 |
| [`useCallback`](./hooks-performance.md) | 返回一个 memoized 回调函数。 |
| [`useContext`](./hooks-other.md) | 接受一个 context 对象并返回其当前值。 |
| [`useDebugValue`](./hooks-other.md) | 在 React DevTools 中为自定义 hook 显示标签。 |
| [`useDeferredValue`](./hooks-performance.md) | 延迟更新 UI 的一部分。 |
| [`useEffect`](./hooks-effect.md) | 允许你在函数组件中执行副作用。 |
| [`useId`](./hooks-other.md) | 生成在服务器和客户端之间保持稳定的唯一 ID。 |
| [`useImperativeHandle`](./hooks-ref.md) | 在使用 `ref` 时，自定义暴露给父组件的实例值。 |
| [`useInsertionEffect`](./hooks-effect.md) | 与 `useEffect` 类似，但在所有 DOM 突变之前同步触发。 |
| [`useLayoutEffect`](./hooks-effect.md) | 在所有 DOM 突变后同步触发。 |
| [`useMemo`](./hooks-performance.md) | 返回一个 memoized 值。 |
| [`useOptimistic`](./hooks-state.md) | 允许你乐观地更新 UI。 |
| [`useReducer`](./hooks-state.md) | `useState` 的替代方案，用于管理复杂的状态逻辑。 |
| [`useRef`](./hooks-ref.md) | 返回一个可变 ref 对象。 |
| [`useState`](./hooks-state.md) | 为函数组件添加 state。 |
| [`useSyncExternalStore`](./hooks-other.md) | 允许你订阅外部存储。 |
| [`useTransition`](./hooks-performance.md) | 允许你在不阻塞 UI 的情况下更新 state。 |

## 组件

这些是可以在 JSX 中使用的内置组件。

| Component | Description |
|---|---|
| [`<Component>`](./core-apis-components-and-props.md) | 当 React 组件使用 ES6 类定义时，它是组件的基类。 |
| [`<Fragment>`](./core-apis-components-and-props.md) | 允许你对子元素列表进行分组，而无需向 DOM 添加额外的节点。 |
| [`<Profiler>`](./core-apis-components-and-props.md) | 测量 React 树的渲染性能以进行优化。 |
| [`<PureComponent>`](./core-apis-components-and-props.md) | 与 `Component` 类似，但通过对 prop 和 state 的浅层比较来实现 `shouldComponentUpdate()`。 |
| [`<StrictMode>`](./core-apis-components-and-props.md) | 用于高亮显示应用程序中潜在问题的工具。 |
| [`<Suspense>`](./advanced-code-splitting.md) | 允许你的组件在渲染前“等待”某些内容，并显示一个 fallback UI。 |

## API

由 `React` 对象提供的顶层 API。

| API | Description |
|---|---|
| [`cache`](./advanced-caching.md) | 用于 memoize 数据获取函数的实用工具。 |
| [`cloneElement`](./core-apis-creating-elements.md) | 以一个元素为起点，克隆并返回一个新的 React 元素。 |
| [`createContext`](./core-apis-context.md) | 创建一个 Context 对象。 |
| [`createElement`](./core-apis-creating-elements.md) | 创建并返回一个给定类型的新 React 元素。 |
| [`createRef`](./core-apis-refs.md) | 创建一个可以附加到 React 元素上的 ref 对象。 |
| [`forwardRef`](./core-apis-refs.md) | 创建一个 React 组件，该组件将其接收到的 ref 属性转发给树中更深层的另一个组件。 |
| [`lazy`](./advanced-code-splitting.md) | 允许你定义一个动态加载的组件。 |
| [`memo`](./hooks-performance.md) | 用于 memoize 组件渲染输出的高阶组件。 |
| [`startTransition`](./advanced-transitions.md) | 允许你将状态更新标记为非紧急的 transition。 |
| [`version`](./api-reference.md) | 包含当前 React 版本的字符串。 |

## 工具函数

用于处理 React 数据结构的辅助函数。

| Utility | Description |
|---|---|
| [`Children`](./core-apis-children-utilities.md) | 一个包含处理 `props.children` 数据结构方法的对象。 |
| [`isValidElement`](./core-apis-creating-elements.md) | 验证对象是否为 React 元素。返回 `true` 或 `false`。 |

## 实验性 API

这些 API 是实验性的，可能会在未来的版本中更改或移除。它们旨在供早期采用和反馈。

有关更多详细信息，请参阅[实验性 API](./advanced-experimental.md) 指南。

| API | Description |
|---|---|
| `experimental_useEffectEvent` | 一个用于从 `useEffect` 中提取非响应式逻辑的实验性 Hook。 |
| `unstable_Activity` | 用于在 transition 期间协调 UI 显示和消失的组件。 |
| `unstable_SuspenseList` | 协调多个 `Suspense` 组件的加载状态。 |
| `unstable_ViewTransition` | 用于创建动画视图 transition 的实验性 API。 |
| `unstable_getCacheForType` | 检索给定类型的缓存实例，与 `cache` 一起使用。 |
| `unstable_useCacheRefresh` | 提供一个使 React 缓存失效的函数。 |
| `unstable_postpone` | 一个用于推迟渲染组件树的实验性函数。 |
| `unstable_LegacyHidden` | 一个用于在不卸载的情况下从 UI 中隐藏子树的组件。 |
