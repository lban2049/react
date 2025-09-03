# 包导出

`react` npm 包提供了多个入口点，以支持不同的环境和工具链。这种结构允许构建工具根据上下文（例如开发环境与生产环境，或客户端渲染与服务端渲染）选择正确的 React 版本。本指南将详细介绍这些导出，解释它们的用途和使用方式。

## 导出项摘要

主要入口点在 React 的 `package.json` 的 `exports` 字段中定义。以下是最重要的一些导出项的摘要：

| 导出路径              | 描述                                                                                              | 注意事项                                                                                             |
| ------------------------ | -------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------- |
| `react`                  | React 核心库的主要入口点。用于导入 `useState` 和 `useEffect` 等 API。    | 根据条件加载开发版或生产版构建。还有一个 `react-server` 变体。          |
| `react/jsx-runtime`      | 用于自动 JSX 转换的生产环境 JSX 运行时。                                              | 由编译器（如 Babel 或 SWC）使用。通常你不需要在代码中直接导入它。    |
| `react/jsx-dev-runtime`  | 开发环境的 JSX 运行时，包含额外的验证和有用的警告。                       | 由编译器在开发模式下使用。                                                            |
| `react/compiler-runtime` | React 编译器的运行时辅助库。                                                       | 这是编译器使用的内部包，不应直接使用。              |
| `react/package.json`     | 暴露包本身的 `package.json` 文件。                                                        | 对于需要检查 React 元数据（如版本）的工具有用。                      |

## 导出项如何解析

当你编写像 `import { useState } from 'react'` 这样的导入语句时，你的构建工具（如 Webpack 或 Vite）或 Node.js 运行时会使用 `exports` 映射来解析它。该过程通常遵循以下步骤：

```d2
direction: down

"你的代码": {
  shape: document
  label: "import { useState } from 'react';"
}

"构建工具": {
  shape: rectangle
  label: "打包工具 / Node.js"
}

"package.json": {
  shape: document
  label: "react/package.json"
}

"入口点脚本": {
  shape: document
  label: "例如，index.js"
}

"最终模块": {
  shape: package
  grid-columns: 2
  "react.development.js": {}
  "react.production.js": {}
}

"你的代码" -> "构建工具": "1. 处理导入"
"构建工具" -> "package.json": "2. 读取 'exports' 映射"
"package.json" -> "入口点脚本": "3. 根据条件（例如 'default'）选择入口"
"入口点脚本" -> "最终模块": "4. 根据 NODE_ENV 选择构建版本"

```

该系统确保你能在特定情况下获得最合适的 React 版本，而无需手动配置。

## 入口点详解

### 核心入口点 (`react`)

这是你每天使用的标准入口点。它提供了所有核心的 React API。

```javascript
import React, { useState, useEffect } from 'react';
```

加载的文件 (`index.js`) 包含根据 `process.env.NODE_ENV` 变量在开发版和生产版构建之间切换的逻辑。

```javascript
// react/index.js 的简化视图
'use strict';

if (process.env.NODE_ENV === 'production') {
  module.exports = require('./cjs/react.production.js');
} else {
  module.exports = require('./cjs/react.development.js');
}
```

- **开发版构建** (`react.development.js`) 体积更大，包含有用的警告和错误信息以帮助调试。
- **生产版构建** (`react.production.js`) 体积更小、经过优化，并移除了仅在开发环境中出现的警告。

### JSX 运行时 (`react/jsx-runtime` 和 `react/jsx-dev-runtime`)

这些入口点支持现代的 JSX 转换。通过这种转换，你不再需要在每个使用 JSX 的文件中导入 `React`。你的编译器会自动从这些运行时中导入必要的函数。

- `react/jsx-runtime` 用于生产版构建。
- `react/jsx-dev-runtime` 用于开发环境，并会向警告中添加源文件信息。

你可能永远不需要手动导入它们；你的构建工具会处理这一切。

### 编译器运行时 (`react/compiler-runtime`)

此入口点包含 React 编译器（曾用名 React Forget）所需的辅助函数。编译器会自动优化你的 React 组件，而此运行时为这些优化提供了必要的支持代码。它不被视为公共 API 的一部分，不应直接导入。

### 服务端和客户端条件

`exports` 映射还定义了一些特殊条件，其中最著名的是 `react-server`。当为 React Server Components 环境构建时，框架和打包工具会使用此条件来解析一组不同的文件。这使得 React 能够为服务端提供专门的构建版本，这些版本与客户端的构建版本不同。

欲了解有关此主题的更多信息，请参阅 [服务端与客户端环境](./advanced-server-vs-client.md) 指南。