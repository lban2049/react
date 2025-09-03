# 服务器与客户端环境

React 旨在不同的 JavaScript 环境中运行，主要包括客户端（在网页浏览器中）和服务器（使用 Node.js 或类似的运行时）。为实现这一目的，`react` 包为每个环境提供了量身定制的、独特的 API 集合。这种分离有助于优化构建过程，并能防止在错误的环境中使用特定于环境的 API。

`react` 包通过其 `package.json` 文件中的条件导出来正式管理这种区别。

## 条件导出

`react` 包使用一种名为“条件导出”的现代模块功能，根据环境来提供不同的文件。当 Webpack 或 Next.js 等构建工具或运行时检测到服务器环境时，便可以使用 `"react-server"` 条件来导入服务器专用的 React 版本及其 API。

下方是 `react/package.json` 中相关定义的简化示例：

```json
{
  "exports": {
    ".": {
      "react-server": "./react.react-server.js",
      "default": "./index.js"
    },
    "./jsx-runtime": {
      "react-server": "./jsx-runtime.react-server.js",
      "default": "./jsx-runtime.js"
    }
  }
}
```

这一机制是 React 实现关注点分离的关键。`default` 导出指向客户端构建版本，其中包含在浏览器中实现交互性所需的所有功能。`react-server` 导出则指向为服务器端渲染而精简的构建版本，其中排除了在服务器上不相关或可能导致错误的 API。

```d2
direction: down

"打包工具 / 运行时": {
  shape: rectangle
}

"react 包": {
  shape: package
  grid-columns: 2
  grid-gap: 80

  "客户端入口 (`index.js`)": {
    label: "客户端环境\n(默认导出)"
    shape: document
    "APIs": {
      "useState": "有状态逻辑"
      "useEffect": "副作用"
      "Component": "类组件"
      "startTransition": "并发 UI"
    }
  }

  "服务器入口 (`react.react-server.js`)": {
    label: "服务器环境\n('react-server' 导出)"
    shape: document
    "APIs": {
      "cache": "服务器端数据缓存"
      "use": "读取 promise/context"
      "Fragment": "分组元素"
      "createElement": "核心元素工厂"
    }
  }
}

"打包工具 / 运行时" -> "react 包": "根据环境解析模块"
```

## API 可用性比较

这两种环境最显著的区别在于可用的 API 集合。客户端环境包含用于交互、有状态逻辑和浏览器特定副作用的 API。而服务器环境提供的 API 集合则更为有限，主要专注于渲染和数据管理。

下方是关键 API 及其可用性的比较。

| API | 客户端环境 | 服务器环境 | 说明 |
|---|---|---|---|
| **State Hooks** | | | |
| `useState`, `useReducer` | ✅ 可用 | ❌ 不可用 | 管理组件状态，这是一个与用户交互和重新渲染相关的客户端概念。 |
| `useActionState`, `useOptimistic` | ✅ 可用 | ❌ 不可用 | 用于管理表单状态和客户端上待处理的 UI 更新的 Hooks。 |
| **Effect Hooks** | | | |
| `useEffect`, `useLayoutEffect`, `useInsertionEffect` | ✅ 可用 | ❌ 不可用 | 用于执行与浏览器 DOM 和生命周期交互的副作用，这些在服务器上不存在。 |
| **组件类型** | | | |
| `Component`, `PureComponent` | ✅ 可用 | ❌ 不可用 | 类组件及其生命周期方法是客户端组件模型的一部分。 |
| **并发 API** | | | |
| `startTransition`, `useTransition`, `useDeferredValue` | ✅ 可用 | ❌ 不可用 | 用于管理并发渲染转换的 API，主要用于在客户端更新期间保持 UI 的响应性。 |
| **共享 Hooks 和 API** | | | |
| `use`, `useId`, `useMemo`, `useCallback`, `useDebugValue` | ✅ 可用 | ✅ 可用 | 不依赖于特定客户端或服务器生命周期的 Hooks。 |
| `cache`, `cacheSignal` | ✅ 可用 | ✅ 可用 | 提供了一种用于记忆化和缓存的机制。其底层实现针对每个特定环境进行了优化。 |
| `createElement`, `cloneElement`, `isValidElement` | ✅ 可用 | ✅ 可用 | 用于创建和操作 React 元素的基础 API 在所有环境中都可用。 |
| `Fragment`, `Profiler`, `StrictMode`, `Suspense` | ✅ 可用 | ✅ 可用 | 用于构建和调试应用程序的核心组件。 |

## 共享内部构件

React 内部为每个环境维护了独立的共享状态对象：`ReactSharedInternalsClient` 和 `ReactSharedInternalsServer`。这些对象保存了 Hooks 及其他功能所需的当前状态，例如 Hooks 的活动分发器或当前的过渡状态。

例如，`ReactSharedInternalsClient` 包含 `actQueue`（用于测试）和 `isBatchingLegacy`（用于旧版模式）等属性，这些属性与服务器无关。相反，`ReactSharedInternalsServer` 则被配置为在启用时处理 Taint API 等安全功能。

虽然你不会直接与这些内部对象交互，但它们的存在对于 React 隔离特定于环境的逻辑并保持清晰的关注点分离至关重要。

## 实际影响

在开发使用服务器端渲染或 React Server Components 的应用程序时，理解这种分离至关重要。当编写一个旨在同时在服务器和客户端上运行的组件时，必须只使用共享的 API 集合。

- **避免在服务器上使用仅限客户端的 API**：在仅于服务器上渲染的组件中尝试使用 `useState` 或 `useEffect` 等 Hook 会导致错误。
- **优化的服务器构建**：这种分离可以实现更小、更高效的服务器构建，因为它排除了用于客户端交互、状态管理和副作用的代码。

通过遵循这些环境边界，你可以构建出既健壮又高效的应用程序，从而充分利用 React 在服务器和客户端上的全部功能。

---

### 后续步骤

探索以下相关主题，进一步了解 React 如何利用不同环境。

<x-cards data-columns="2">
  <x-card data-title="缓存" data-icon="lucide:database" data-href="/advanced/caching">
    了解 React 的缓存功能如何在客户端和服务器上用于数据获取和记忆化。
  </x-card>
  <x-card data-title="使用 lazy 和 Suspense 进行代码分割" data-icon="lucide:splitsquare-horizontal" data-href="/advanced/code-splitting">
    探索如何通过仅在需要时加载组件来提高应用性能，这是一种在两种环境中都与 Suspense 配合使用的模式。
  </x-card>
</x-cards>