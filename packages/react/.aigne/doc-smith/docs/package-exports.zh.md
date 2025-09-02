# 包导出

`react` npm 包被设计用于在不同环境和构建设置中实现通用性。除了大多数开发者使用的主要 `import React from 'react'` 之外，该包还公开了其他几个入口点。这些入口点在 `package.json` 的 `exports` 字段中被正式定义，这允许构建工具和 JavaScript 运行时为给定环境选择正确的文件。

理解这些导出对于配置构建工具、优化包以及在特定上下文（如服务器环境）中利用 React 功能非常有用。

## 主要入口点

`react` 包为其核心功能、JSX 运行时和编译器运行时提供了不同的入口点。其中许多入口点还为客户端和服务器环境提供了不同版本，这对于像 React Server Components 这样的功能至关重要。

以下是可用的主要导出的细分：

| Export Path | Description |
|---|---|
| `react` | React 库的主要入口点。它提供了像 `useState`、`useEffect` 等核心 API。它会为服务器和客户端环境有条件地加载不同的模块。 |
| `react/jsx-runtime` | JSX 运行时。现代构建工具使用它来自动将 JSX 转换为函数调用，而无需将 `React` 引入作用域。这是生产构建的默认设置。 |
| `react/jsx-dev-runtime` | 一个专注于开发者的 JSX 运行时版本。它包含额外的验证和警告，以帮助在开发过程中捕获潜在问题。 |
| `react/compiler-runtime` | 提供 React 编译器所需的运行时辅助函数。该模块通常在项目中启用编译器时自动使用，而不是由开发者直接导入。 |

## 开发与生产构建版本

每个入口点都会根据 `process.env.NODE_ENV` 环境变量动态选择一个文件。当 `NODE_ENV` 设置为 `'production'` 时，将使用优化的生产构建版本。否则，将加载包含有用警告和调试工具的开发构建版本。

这种机制在所有入口点中都是一致的，正如它们各自的文件所示：

```javascript
// react/index.js 的内容
'use strict';

if (process.env.NODE_ENV === 'production') {
  module.exports = require('./cjs/react.production.js');
} else {
  module.exports = require('./cjs/react.development.js');
}
```

下图说明了这种条件加载流程：

```d2
direction: down

"import 'react'": {
  shape: step
  label: "应用程序或库导入一个入口点，例如 'react'"
}

"check_env": {
  shape: diamond
  label: "process.env.NODE_ENV === 'production'？"
}

"prod_module": {
  shape: package
  label: "加载优化的生产模块\n（例如 react.production.js）"
  style.fill: "#E5F9E0"
}

"dev_module": {
  shape: package
  label: "加载带有警告的开发模块\n（例如 react.development.js）"
  style.fill: "#FEF3C7"
}

"import 'react'" -> "check_env"
"check_env" -> "prod_module": "是"
"check_env" -> "dev_module": "否"
```

## 服务器与客户端环境

`package.json` 中的 `exports` 映射还定义了一个 `"react-server"` 条件。这允许为服务器环境配置的打包器（例如，用于渲染 React Server Components）解析到一组不同的文件，例如 `react.react-server.js` 或 `jsx-runtime.react-server.js`。

这种分离确保了仅服务器和仅客户端的代码不会被打包到错误的环境中，这是现代 React 框架运作的基础。

要更深入地了解此主题，请参阅[服务器与客户端环境](./advanced-server-vs-client.md)指南。

## 如何使用这些导出

在大多数现代 React 项目中，你不会直接导入这些子路径。相反，你的构建工具会配置为替你处理它们。

例如，Babel 或 TypeScript 中的“自动”JSX 转换被配置为在后台导入 `react/jsx-runtime` 或 `react/jsx-dev-runtime`。这就是为什么你可以在一个文件中编写 JSX 而无需 `import React from 'react'`。

```json
// .babelrc 配置示例（概念性）
{
  "presets": [
    [
      "@babel/preset-react",
      {
        "runtime": "automatic" // 这告诉 Babel 使用 react/jsx-runtime
      }
    ]
  ]
}
```

通过理解这些包导出，你可以深入了解 React 生态系统是如何构建的，以支持从简单的客户端应用程序到复杂的服务器渲染体验等各种平台和开发需求。