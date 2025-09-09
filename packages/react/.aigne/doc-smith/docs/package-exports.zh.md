# 包导出

The `react` 包专为现代 JavaScript 环境和打包工具设计。它利用 Node.js 包导出（Package Exports）功能提供多个入口点，允许工具根据不同用例和环境（例如开发环境与生产环境，或客户端与服务器端）选择合适的库版本。

了解这些导出项有助于配置构建工具，或在需要与 React 生态系统的特定部分（如 JSX 运行时）交互的高级用例中发挥作用。

## 主要入口点

`react` 包在其 `package.json` 中定义了多个入口点。以下是你会遇到的主要导出项的摘要：

| 导出路径 | 描述 |
|---|---|
| `react` | React 核心库的主要入口点。当你编写 `import React from 'react'` 时，获取的就是这个。它提供了对 `useState`、`useEffect`、`createElement` 等 API 的访问。 |
| `react/jsx-runtime` | 生产环境的 JSX 运行时。编译器（如 Babel 或 TypeScript）使用此入口点进行自动 JSX 转换，将 JSX 语法转换为 `jsx()` 函数调用。通常你不需要直接导入它。 |
| `react/jsx-dev-runtime` | 开发版本的 JSX 运行时。它包含额外的警告和调试信息，例如源文件和行号跟踪，这在开发过程中很有帮助。 |
| `react/compiler-runtime` | 包含 React 编译器所需的运行时辅助函数。此代码由编译器在需要时自动注入，不应由开发人员直接使用。 |
| `react/package.json` | 暴露包的 `package.json` 文件，这对于需要检查包元数据的工具可能很有用。 |

---

## 条件导出

React 使用条件导出根据环境提供不同的文件。这确保了你在生产环境中获得最佳性能，同时在开发期间拥有有用的警告和工具。

### 开发构建与生产构建

大多数入口点，包括主 `react` 导出和 JSX 运行时，都会检查 `process.env.NODE_ENV` 变量。根据其值，它们会加载开发构建或生产构建。

- **开发环境 (`NODE_ENV !== 'production'`)**：开发构建版本体积更大，包含大量警告、错误消息和调试辅助功能。
- **生产环境 (`NODE_ENV === 'production'`)**：生产构建版本经过压缩和性能优化，所有仅用于开发的代码都被移除。

以下是主 `index.js` 入口点的逻辑，它说明了这种模式：

```javascript index.js icon=logos:javascript
'use strict';

if (process.env.NODE_ENV === 'production') {
  module.exports = require('./cjs/react.production.js');
} else {
  module.exports = require('./cjs/react.development.js');
}
```

你的构建工具（如 Webpack、Vite 或 Parcel）负责正确设置 `process.env.NODE_ENV`，从而为你的生产应用自动启用这些优化。

### React 服务器环境与客户端环境

`exports` 映射还包含一个 `"react-server"` 条件。这使得支持 React Server Components 的打包工具和框架能够解析到专为在仅服务器环境中运行而设计的特定 React 构建版本。

例如，主 `.` 导出定义如下：

```json package.json icon=logos:npm
"exports": {
  ".": {
    "react-server": "./react.react-server.js",
    "default": "./index.js"
  },
  // ...other exports
}
```

在理解 `"react-server"` 条件的环境中，导入 `react` 将解析为 `react.react-server.js`。在所有其他环境（如传统的客户端应用程序）中，它将解析为 `default` 导出，即 `index.js`。

同样的条件逻辑也适用于 JSX 运行时，确保无论你的组件在服务器上还是在客户端上渲染，都能使用正确的版本。要深入了解此主题，请参阅我们的指南 [服务器环境与客户端环境](./advanced-server-vs-client.md)。
