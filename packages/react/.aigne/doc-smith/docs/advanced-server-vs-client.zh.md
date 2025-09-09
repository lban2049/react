# 服务器与客户端环境

React 旨在构建可同时在服务器和客户端（通常是 Web 浏览器）上渲染的用户界面。这种灵活性是现代 React 架构的基石，它催生了服务器端渲染 (SSR) 和 React 服务器组件等模式。理解这两种环境之间的区别是充分发挥 React 潜力的关键，因为每种环境都有权访问一套根据其特定用途定制的不同 API。

## 双重入口点

`react` 包通过为构建工具提供不同的入口点，从而正式确立了这种分离。这一点在其 `package.json` 文件的 `exports` 映射中有明确定义。当构建工具在服务器环境（例如，对于 React 服务器组件）中遇到 `react` 的导入时，它会解析到特定于服务器的文件。

```json package.json icon=logos:npm
{
  "exports": {
    ".": {
      "react-server": "./react.react-server.js",
      "default": "./index.js"
    },
    "./jsx-runtime": {
      "react-server": "./jsx-runtime.react-server.js",
      "default": "./jsx-runtime.js"
    },
    "./jsx-dev-runtime": {
      "react-server": "./jsx-dev-runtime.react-server.js",
      "default": "./jsx-dev-runtime.js"
    }
    // ... other exports
  }
}
```

此配置可确保仅为每个环境打包必要的代码，从而优化应用程序的性能。

```d2
direction: down

react-package: {
  label: "'react' NPM 包"
  shape: rectangle

  client-entry: {
    label: "index.js\n(默认入口)"
  }

  server-entry: {
    label: "react.react-server.js\n('react-server' 入口)"
  }
}

client-env: {
  label: "客户端环境\n(浏览器)"
  shape: rectangle
  APIs: {
    label: "用于交互性的 API"
    "- useState()\n- useEffect()\n- useTransition()\n- Component Class"
  }
}

server-env: {
  label: "服务器环境\n(例如 Node.js)"
  shape: rectangle
  APIs: {
    label: "用于渲染和数据的 API"
    "- cache()\n- use() 用于 Promise\n- 无状态或副作用"
  }
}

react-package.client-entry -> client-env: "解析为"
react-package.server-entry -> server-env: "解析为"
```

## 核心 API 差异

最显著的差异在于可用的 API 集合。服务器环境专注于非交互式渲染和数据获取，而客户端环境则增加了用于状态管理、副作用和用户交互的 API。

下面是一些关键 API 及其可用性的比较：

| API 名称 | 服务器端可用 | 客户端可用 | 说明 |
| :--- | :---: | :---: | :--- |
| `createElement` | ✅ | ✅ | 创建 React 元素的基础 API。 |
| `Fragment`, `Profiler`, `StrictMode`, `Suspense` | ✅ | ✅ | 用于构建和调试应用程序的核心组件。 |
| `useState`, `useReducer`, `useActionState` | ❌ | ✅ | 用于管理组件状态的 Hook。在服务器端不可用。 |
| `useEffect`, `useLayoutEffect`, `useInsertionEffect` | ❌ | ✅ | 用于执行副作用的 Hook。仅限客户端。 |
| `useRef`, `useImperativeHandle` | ❌ | ✅ | 用于引用 DOM 节点或组件实例的 Hook。 |
| `useContext` | ❌ | ✅ | 用于消费 context 的 Hook。对于客户端状态传播至关重要。 |
| `Component`, `PureComponent` | ❌ | ✅ | 类组件是仅限客户端的功能。 |
| `useTransition`, `startTransition` | ❌ | ✅ | 用于管理非阻塞 UI 更新的 API。 |
| `cache` | ✅ | ✅ | 用于记忆化数据获取。虽然在两端都可用，但主要设计用于服务器组件内的服务器端数据获取。 |
| `use` | ✅ | ✅ | 一个多功能的 Hook，可以读取 promise 的值（服务器和客户端）或 context 的值（仅限客户端）。 |

## 两种不同的内部实现

这种分离延伸到 React 内部工作机制的深处。每个环境都使用一个独特的共享状态对象（`ReactSharedInternalsServer` vs. `ReactSharedInternalsClient`），以满足其特定需求。

例如，服务器的内部状态包含用于安全功能（如污点预防）的逻辑，这在客户端上是不相关的。相反，客户端的内部状态则管理着 `act` 测试工具的过渡状态和队列。

这种内部分歧表明，这两种环境不仅仅是彼此的子集，而是为不同任务优化的、真正独立的运行时。

## 结论

总而言之，选择正确的 API 完全取决于你的组件将在何处运行。服务器组件可以利用服务器端独有的功能，如直接访问数据库和 `cache` 函数，但不能使用状态或副作用。客户端组件通常用 `"use client"` 指令标记，在浏览器中执行，并可以访问全套 Hook，以构建丰富的交互式体验。

要了解这些环境如何协同工作以优化应用程序的加载性能，请深入阅读我们的[使用 `lazy` 和 `Suspense` 进行代码分割](./advanced-code-splitting.md)指南。