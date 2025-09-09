# API 参考

本页面为 `react` 包中导出的所有公共 API 提供了全面的参考。它涵盖了内置组件、Hook 以及适用于客户端和服务器环境的各种实用工具。如需更深入的解释和用法示例，请参阅 [核心 API](./core-apis.md)、[Hook](./hooks.md) 和 [高级指南](./advanced.md) 部分中的具体指南。

## 组件

这些是您可以在 JSX 中使用的内置组件。

| Component | Description |
|---|---|
| `Component` | 作为 ES6 类定义的 React 组件的基类。 |
| `PureComponent` | 与 `Component` 类似，但通过对 prop 和 state 进行浅层比较来实现 `shouldComponentUpdate()`。 |
| `Fragment` | 允许您将子元素列表分组，而无需向 DOM 添加额外的节点。可以写成 `<>...</>`。 |
| `Profiler` | 测量 React 树的渲染性能，用于性能优化。 |
| `StrictMode` | 一个用于突出显示应用程序中潜在问题的工具。它会为其后代激活额外的检查和警告。 |
| `Suspense` | 允许您在子组件加载完成前显示一个备用 UI。用于代码分割和数据获取。 |

## Hook

Hook 允许您在函数组件中使用 state 和其他 React 功能。它们可以按用途分类，例如 State、Effect 和 Performance。有关每个 Hook 的详细指南，请访问主要的 [Hook 文档](./hooks.md)。

| Hook | Description |
|---|---|
| `useActionState` | 一个用于管理表单操作状态的 Hook。 |
| `useCallback` | 返回一个记忆化的回调函数，可用于性能优化。 |
| `useContext` | 接受一个 context 对象（从 `React.createContext` 返回的值），并返回该 context 的当前值。 |
| `useDebugValue` | 可用于在 React DevTools 中为自定义 Hook 显示标签。 |
| `useDeferredValue` | 推迟更新部分 UI，有助于在大量计算期间保持界面响应。 |
| `useEffect` | 接受一个包含命令式、可能有副作用的代码的函数。它在渲染提交到屏幕后运行。 |
| `useId` | 一个用于生成在服务器和客户端之间保持稳定的唯一 ID 的 Hook。 |
| `useImperativeHandle` | 在使用 `ref` 时，自定义暴露给父组件的实例值。 |
| `useInsertionEffect` | 与 `useEffect` 类似，但它在所有 DOM 变更*之前*同步触发。可用于 CSS-in-JS 库。 |
| `useLayoutEffect` | 在所有 DOM 变更后同步触发。使用此 Hook 可以从 DOM 读取布局并同步重新渲染。 |
| `useMemo` | 返回一个记忆化的值，仅在某个依赖项发生变化时才重新计算。 |
| `useOptimistic` | 一个允许您乐观地更新 UI 的 Hook，假设数据提交将会成功。 |
| `useReducer` | `useState` 的替代方案。接受一个类型为 `(state, action) => newState` 的 reducer，并返回当前的 state 及与之配对的 `dispatch` 方法。 |
| `useRef` | 返回一个可变的 ref 对象，其 `.current` 属性被初始化为传入的参数。 |
| `useState` | 一个允许您向函数组件添加 React state 的 Hook。 |
| `useSyncExternalStore` | 一个允许您订阅外部 store，确保 UI 一致性的 Hook。 |
| `useTransition` | 一个将状态更新标记为非紧急的 Hook，以防止它们阻塞用户输入。 |
| `use` | 一个允许您读取 Promise 或 context 等资源值的 Hook。 |

## 实用工具

这些是用于处理 React 元素和组件的顶层实用工具。

| Utility | Description |
|---|---|
| `Children` | 提供用于处理 `props.children` 这个不透明数据结构的实用工具。包括 `Children.map`、`Children.forEach` 等。 |
| `cloneElement` | 以一个元素为起点，克隆并返回一个新的 React 元素。 |
| `createElement` | 创建并返回一个给定类型的新 React 元素。JSX 会编译成对此函数的调用。 |
| `isValidElement` | 验证对象是否为 React 元素。返回 `true` 或 `false`。 |
| `version` | 包含当前 React 版本的字符串。 |
| `act` | 一个测试实用工具，可确保在进行断言之前，与状态更改相关的所有更新都已处理并应用于 DOM。（仅限开发环境） |

## API

这些顶层 API 用于创建 context、ref 和高阶组件。

| API | Description |
|---|---|
| `createContext` | 创建一个 Context 对象。组件可以订阅此 context 以读取其值。 |
| `createRef` | 创建一个 ref 对象，该对象可以通过 `ref` 属性附加到 React 元素。 |
| `forwardRef` | 创建一个 React 组件，该组件将其接收到的 `ref` 属性转发给树中更深层的另一个组件。 |
| `lazy` | 允许您定义一个动态加载的组件。用于代码分割。 |
| `memo` | 一个用于记忆化组件渲染输出的高阶组件，以防止在 props 相同的情况下重新渲染。 |
| `startTransition` | 一个允许您将状态更新标记为非紧急 transition 的函数。 |
| `cache` | 一个用于缓存数据获取或计算结果的函数。 |

## 不稳定和实验性 API

> **警告**
> 这些 API 尚不稳定，可能会在未来的版本中被更改或删除。在生产应用程序中使用它们需要您自担风险。

| API | Description |
|---|---|
| `unstable_Activity` | 一个用于协调屏幕外内容 transition 的组件。 |
| `unstable_SuspenseList` | 帮助协调多个 `Suspense` 组件的加载状态。 |
| `unstable_LegacyHidden` | 一个将其子组件从视图中隐藏但保留其状态的组件。 |
| `unstable_Scope` | 一个用于创建作用域事件系统的实验性组件。 |
| `unstable_TracingMarker` | 一个用于标记性能追踪的实验性组件。 |
| `unstable_ViewTransition` | 一个用于协调视图 transition 的实验性组件。 |
| `unstable_useCacheRefresh` | 一个用于使 React `cache` 失效的实验性 Hook。 |
| `unstable_getCacheForType` | 一个用于访问给定类型的缓存实例的实验性函数。 |
| `unstable_addTransitionType` | 一个用于向 transition 添加元数据的实验性函数。 |
| `experimental_useEffectEvent` | 一个用于从 `useEffect` 中提取非响应式逻辑的实验性 Hook。 |