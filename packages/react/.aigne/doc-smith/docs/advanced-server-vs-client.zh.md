# 服务器环境与客户端环境

React 旨在用于在各种环境中构建用户界面。主要区别在于**服务器**和**客户端**（浏览器）。这种分离实现了强大的渲染策略，例如服务器端渲染 (SSR) 和 React Server Components 的使用。虽然大部分 React API 是通用的，但某些函数和钩子是特定环境独有的。本指南将探讨这些差异以及 React 是如何管理它们的。

## 条件包导出

环境之间的区别通过条件导出在包级别进行管理。当你从 `react` 包导入时，你的构建工具或运行时会根据是为服务器环境还是客户端环境构建，解析到不同的文件。这个逻辑定义在 React 的 `package.json` 文件中：

```json
// A simplified view of react/package.json
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

`react-server` 条件指向服务器专用的构建版本，而 `default` 通常解析为用于浏览器的客户端构建版本。

这种机制允许 React 为每种上下文提供一组优化的 API。

```d2
direction: down

Bundler: "构建工具或运行时"

package_json: {
  shape: document
  label: "react/package.json"
}

condition_check: {
  shape: diamond
  label: "这是 'react-server' 环境吗？"
}

server_entry: {
  label: "加载 './react.react-server.js' (服务器 API)"
  style: {
    fill: "#d3e5ff"
  }
}

client_entry: {
  label: "加载 './index.js' (客户端 API)"
  style: {
    fill: "#d0f0c0"
  }
}

Bundler -> package_json: "读取 'exports' 字段"
package_json -> condition_check: "评估条件"
condition_check -> server_entry: "是"
condition_check -> client_entry: "否 (使用 'default')"
```

## API 可用性

虽然许多核心概念（如组件、元素和一些钩子）是通用的，但有相当数量的 API 是特定于环境的。客户端环境拥有一套更丰富的 API，用于实现交互性、状态管理以及直接操作 DOM 的副作用。

| API | Environment(s) | Description |
|---|---|---|
| **Universal APIs** | | |
| `createElement`, `cloneElement`, `isValidElement` | Server & Client | 用于创建和使用 React 元素的核心函数。 |
| `Fragment`, `Profiler`, `StrictMode`, `Suspense` | Server & Client | 用于构建 UI 和管理渲染行为的内置组件。 |
| `useMemo`, `useCallback`, `useDebugValue` | Server & Client | 用于优化和调试的钩子，在两个运行时中都可用。 |
| `useId` | Server & Client | 生成稳定、唯一的 ID，可安全地用于服务器渲染。 |
| `use` | Server & Client | 读取 Promise 或上下文等资源的值。 |
| `lazy`, `memo`, `forwardRef` | Server & Client | 用于优化组件和转发 ref 的实用工具。 |
| **Client-Only APIs** | | |
| `useState`, `useReducer`, `useRef`, `useActionState`, `useOptimistic` | Client | 用于管理状态、复杂状态逻辑以及对 DOM 元素或值的引用的钩子。 |
| `useEffect`, `useLayoutEffect`, `useInsertionEffect`| Client | 用于执行与浏览器环境交互的副作用（例如，数据获取、订阅、DOM 修改）的钩子。 |
| `useContext` | Client | 用于读取和订阅上下文的钩子。 |
| `startTransition`, `useTransition` | Client | 用于将 UI 更新标记为非紧急，以避免阻塞用户输入的 API。 |
| `Component`, `PureComponent` | Client | 用于创建有状态类组件的基类。 |
| `createContext` | Client | 创建一个 Context 对象，用于在不使用 prop 逐层传递的情况下共享数据。 |
| `act` | Client | 一种测试实用工具，可确保在进行断言之前处理完更新。 |
| **Server-Only APIs** | | |
| `cache`, `cacheSignal` | Server | 一个用于在单次服务器请求中跨组件渲染对数据获取或计算进行记忆化的函数。`cacheSignal` 是一个相关的实用工具。 |
| `__SERVER_INTERNALS_DO_NOT_USE_OR_WARN_USERS_THEY_CANNOT_UPGRADE` | Server | 一个暴露服务器特定状态的内部对象，不应直接使用。 |

## 内部状态差异

除了公开的 API，React 管理的内部状态也有所不同。每个环境都有一个独特的 `ReactSharedInternals` 对象，以满足其特定需求。

- **服务器内部 (`ReactSharedInternalsServer`)**: 关注请求级别的问题。例如，当 `enableTaint` 激活时，它会包含用于实验性安全功能的注册表 (`TaintRegistryObjects`, `TaintRegistryValues`)，这有助于防止服务器数据泄露。

- **客户端内部 (`ReactSharedInternalsClient`)**: 管理与浏览器交互性和渲染相关的状态。这包括用于 `act` 测试实用工具的队列 (`actQueue`)、用于并发转换的状态 (`asyncTransitions`) 以及用于在渲染批处理中跟踪异步工作的标志 (`didUsePromise`)。

## 总结

理解 React 的服务器和客户端环境之间的区别是构建现代化、高性能应用程序的关键。这种双运行时架构使你能够充分利用两者的优势：服务器的快速初始页面加载和客户端的丰富交互性。通过了解在每种上下文中哪些 API 可用，你可以编写出更高效、更健壮的代码。

### 后续步骤

要了解更多利用此架构的功能，请浏览以下指南：

<x-cards>
  <x-card data-title="缓存" data-icon="lucide:database-zap" data-href="/advanced/caching">
    深入了解 React 的缓存机制，这些机制在服务器和客户端上都可用，但有不同的用例。
  </x-card>
  <x-card data-title="使用 lazy 和 Suspense 进行代码分割" data-icon="lucide:split" data-href="/advanced/code-splitting">
    学习如何使用 `Suspense` 来处理服务器上的数据获取等异步操作，以及客户端上的代码分割。
  </x-card>
</x-cards>