# 服务器环境 vs. 客户端环境

React 可以在不同环境中使用，主要是在服务器上进行初始渲染，以及在客户端（浏览器中）进行交互。`react` 包旨在支持这两种环境，为每种环境的能力和要求提供了不同的 API 集合。这种区别通过包的 `package.json` 中的条件导出来管理，这确保了你只打包目标环境所需的代码。

本指南将分解服务器和客户端环境及其各自 API 之间的主要区别。

```d2
direction: down

Server-Environment: {
  label: "服务器环境\n(例如，Node.js)"
  shape: rectangle

  APIs: {
    shape: package
    label: "服务器 API"
    grid-columns: 2

    createElement: "createElement"
    use: "use"
    cache: "cache"
    useMemo: "useMemo"
  }

  Responsibilities: {
    label: "职责"
    Data-Fetching: "数据获取"
    Initial-Render: "初始渲染"
    Security: "安全性 (Tainting)"
  }
}

Client-Environment: {
  label: "客户端环境\n(浏览器)"
  shape: rectangle

  APIs: {
    shape: package
    label: "客户端 API (超集)"
    grid-columns: 2

    All-Server-APIs: "所有服务器 API"
    useState: "useState"
    useEffect: "useEffect"
    useTransition: "useTransition"
  }
   Responsibilities: {
    label: "职责"
    Interactivity: "交互性"
    State-Management: "状态管理"
    DOM-Updates: "DOM 更新"
  }
}

Server-Environment -> Client-Environment: "发送渲染后的输出以进行 hydrate"
```

## 服务器环境

服务器环境入口点 (`react/react-server.js`) 是为在服务器上渲染组件而设计的。这是服务器端渲染 (SSR) 和 React 服务器组件 (RSC) 等模式的基础。其 API 范围被有意地限制在不依赖浏览器 DOM 或用户交互的功能上。

服务器环境的主要特点：
- **无状态或副作用：** 管理状态 (`useState`, `useReducer`) 或副作用 (`useEffect`, `useLayoutEffect`) 的 Hook 不可用。这些 Hook 从根本上与浏览器中的组件生命周期和用户交互绑定。
- **专注于数据获取：** 提供了像 `cache` 这样的 API，用于在组件渲染过程中高效地处理服务器端数据获取和记忆化。
- **安全特性：** 包括诸如 Taint Registry 之类的内部机制，以防止敏感的服务器数据无意中暴露给客户端。

服务器上可用的 API 包括：
- `Children` 实用工具 (`map`, `forEach` 等)
- `Fragment`, `Profiler`, `StrictMode`, `Suspense`
- `createElement`, `cloneElement`, `isValidElement`
- `createRef`, `forwardRef`
- `lazy`, `memo`
- `cache`, `cacheSignal`
- Hooks: `use`, `useId`, `useCallback`, `useDebugValue`, `useMemo`

## 客户端环境

客户端环境是 React 的传统运行环境，在用户的浏览器中运行。它包括服务器环境的所有 API，外加一套用于管理状态、处理副作用以及直接与 DOM 交互的完整工具。

客户端环境的主要特点：
- **完全的交互性：** 提供对构建丰富的交互式用户界面所需的完整 Hook 套件 (`useState`, `useEffect`, `useContext` 等) 和类组件特性 (`Component`, `PureComponent`) 的访问。
- **DOM 管理：** 包括像 `createContext` 和 `useRef` 这样的 API，用于管理 DOM 树并与之交互。
- **并发特性：** 支持并发渲染，通过 `useTransition` 和 `startTransition` 等 API，在复杂更新期间保持 UI 响应。

客户端环境独有的 API 包括：
- `Component`, `PureComponent`
- `createContext`
- `postpone`
- 用于状态的 Hooks: `useState`, `useReducer`, `useOptimistic`, `useActionState`
- 用于副作用的 Hooks: `useEffect`, `useLayoutEffect`, `useInsertionEffect`
- 用于 refs 的 Hooks: `useRef`, `useImperativeHandle`
- 用于 context 的 Hooks: `useContext`
- 用于并发的 Hooks: `useTransition`, `useDeferredValue`
- 其他客户端特定的 Hooks: `useSyncExternalStore`, `useCacheRefresh`
- 过渡管理: `startTransition`

## API 可用性比较

下表清晰地比较了关键 React API 的可用位置。

| API | 服务器 | 客户端 | 说明 |
|---|---|---|---|
| `createElement`, `cloneElement` | ✅ | ✅ | 在任何环境中创建 React 元素的基础。 |
| `useState`, `useReducer` | ❌ | ✅ | 管理状态，这是一个与交互性相关的客户端问题。 |
| `useEffect`, `useLayoutEffect` | ❌ | ✅ | 管理与浏览器中组件生命周期相关的副作用（例如，数据获取、订阅）。 |
| `useContext` | ❌ | ✅ | 读取并订阅 context。Provider 可以在服务器上渲染，但消费在客户端进行。 |
| `useMemo`, `useCallback` | ✅ | ✅ | 用于通过记忆化进行性能优化，适用于两种环境。 |
| `useRef` | ❌ | ✅ | 提供对 DOM 元素或在多次渲染之间保持不变的值的可变引用。 |
| `use` | ✅ | ✅ | 可用于读取 Promise 或 context 的值，设计为同构工作。 |
| `cache` | ✅ | ✅ | 一个服务器优先的 API，用于缓存数据请求，但客户端版本也存在。 |
| `Component`, `PureComponent` | ❌ | ✅ | 类组件及其状态和生命周期方法仅限客户端使用。 |
| `lazy` and `Suspense` | ✅ | ✅ | `Suspense` 在服务器和客户端都可用于异步操作。`lazy` 用于客户端代码分割。 |
| `startTransition`, `useTransition` | ❌ | ✅ | 管理非紧急的 UI 更新，是客户端并发渲染模型的核心部分。 |

## 后续步骤

理解这些环境差异对于构建现代化、高性能的 React 应用程序至关重要。要深入了解相关主题，请探索 React 如何处理数据获取和记忆化。

<x-card data-title="缓存" data-icon="lucide:database-zap" data-href="/advanced/caching" data-cta="了解缓存">
了解如何在客户端和服务器上使用 React 的缓存功能进行数据获取。
</x-card>