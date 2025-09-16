# API 参考

`react` 包提供了在 React 应用中定义和管理组件所需的核心 API。本节是所有公共 API 的全面参考，包括 Hooks、内置组件、类和顶层实用工具。

无论你是使用现代函数式组件还是传统的基于类的组件进行构建，都可以在以下部分找到所需的详细信息。

<x-cards data-columns="2">
  <x-card data-title="Hooks" data-icon="lucide:anchor" data-href="/api-reference/hooks">
    所有内置 React Hooks（如 useState、useEffect 和 useContext）的详细文档，并为每个 Hook 提供了使用示例。
  </x-card>
  <x-card data-title="组件和类" data-icon="lucide:box" data-href="/api-reference/components">
    内置组件和类（如 Component、PureComponent、Fragment 和 Suspense）的参考，它们构成了 React 应用的基础。
  </x-card>
  <x-card data-title="顶层 API" data-icon="lucide:function-square" data-href="/api-reference/top-level">
    顶层 React 函数（如 createElement、createContext、forwardRef、lazy 和 memo）的文档，用于各种高级模式和优化。
  </x-card>
  <x-card data-title="React.Children" data-icon="lucide:cuboid" data-href="/api-reference/children">
    使用 React.Children 工具处理 props.children 数据结构的指南，该工具支持对子元素进行强大的操作和检查。
  </x-card>
</x-cards>

## 完整导出列表

为了快速概览，下表列出了从 `react` 包导出的所有主要公共 API。

| API                  | 类别                | 描述                                                                                             |
| -------------------- | ----------------------- | ------------------------------------------------------------------------------------------------------- |
| `Component`            | 组件和类    | 使用 ES6 类时 React 组件的基类。                                             |
| `PureComponent`        | 组件和类    | 与 `Component` 类似，但内置了浅层 prop 和 state 比较以防止重新渲染。    |
| `Fragment`             | 组件和类    | 允许你对一组子元素进行分组，而无需向 DOM 添加额外的节点。                                |
| `Profiler`             | 组件和类    | 出于优化目的，测量 React 树的渲染性能。                               |
| `StrictMode`           | 组件和类    | 一个用于在开发过程中高亮显示应用中潜在问题的工具。                        |
| `Suspense`             | 组件和类    | 允许你为组件树中尚未准备好渲染的部分指定加载指示器。 |
| `use`                  | Hook                    | 一个用于读取资源（如 Promise 或 context）值的 Hook。                               |
| `useCallback`          | Hook                    | 返回一个 memoized（记忆化）的回调函数。                                                                   |
| `useContext`           | Hook                    | 接受一个 context 对象并返回其当前值。                                                 |
| `useDebugValue`        | Hook                    | 在 React DevTools 中为自定义 hook 显示标签。                                                    |
| `useDeferredValue`     | Hook                    | 推迟更新 UI 的一部分。                                                                       |
| `useEffect`            | Hook                    | 允许你在函数组件中执行副作用。                                                   |
| `useId`                | Hook                    | 一个用于生成在服务端和客户端渲染之间保持稳定的唯一 ID 的 Hook。                    |
| `useImperativeHandle`  | Hook                    | 在使用 `ref` 时，自定义暴露给父组件的实例值。                    |
| `useInsertionEffect`   | Hook                    | 与 `useEffect` 类似，但它在所有 DOM 变更之前同步触发。                            |
| `useLayoutEffect`      | Hook                    | 在所有 DOM 变更之后同步触发。                                                            |
| `useMemo`              | Hook                    | 返回一个 memoized（记忆化）的值。                                                                               |
| `useOptimistic`        | Hook                    | 一个允许你乐观地更新 UI 的 Hook。                                                      |
| `useReducer`           | Hook                    | `useState` 的替代方案，用于管理复杂的状态逻辑。                                          |
| `useRef`               | Hook                    | 返回一个可变 ref 对象。                                                                           |
| `useState`             | Hook                    | 一个允许你向函数组件添加 state 的 Hook。                                                  |
| `useSyncExternalStore` | Hook                    | 一个允许你订阅外部存储的 Hook。                                                    |
| `useTransition`        | Hook                    | 一个将状态更新标记为非紧急的 Hook，以改善慢速渲染下的用户体验。               |
| `useActionState`       | Hook                    | 一个用于管理表单操作状态的 Hook。                                                            |
| `cloneElement`         | 顶层 API           | 以一个元素为起点，克隆并返回一个新的 React 元素。                          |
| `createContext`        | 顶层 API           | 创建一个 Context 对象，用于在组件树中传递数据。                                   |
| `createElement`        | 顶层 API           | 创建并返回一个新的 React 元素。通常通过 JSX 使用。                                          |
| `createRef`            | 顶层 API           | 创建一个 ref，可以通过 ref 属性附加到 React 元素上。                             |
| `forwardRef`           | 顶层 API           | 创建一个将 ref 转发给子组件的组件。                                           |
| `isValidElement`       | 顶层 API           | 验证一个对象是否为 React 元素。                                                               |
| `lazy`                 | 顶层 API           | 允许你定义一个动态加载（代码分割）的组件。                                |
| `memo`                 | 顶层 API           | 一个高阶组件，可以 memoize（记忆化）一个组件，在其 props 未改变时防止重新渲染。 |
| `startTransition`      | 顶层 API           | 允许你将状态更新标记为非紧急。                                                              |
| `Children`             | React.Children          | 一个包含用于处理 `props.children` 的工具的对象。                                             |
| `version`              | 包信息     | 一个包含当前 React 版本的字符串。                                                       |

---

本参考提供了 `react` 包中可用 API 的高级概述。有关深入解释和代码示例，请浏览每个类别的详细指南。

要开始使用构建现代应用最常用的 API，请接着阅读 [Hooks](./api-reference-hooks.md) 文档。
