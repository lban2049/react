# 包导出

`react` npm 包暴露了几个不同的入口点，以支持不同的环境和构建配置。这些导出定义在包的 `package.json` 文件中，允许打包工具和框架等工具自动选择正确的 React 版本——例如，在开发和生产构建之间切换，或为 JSX 和服务端环境提供专门的运行时。

## 主要入口点

`react` 包为各种运行时和模式提供了不同的入口点。

<x-cards data-columns="2">
  <x-card data-title="react" data-icon="lucide:box">
    React 库的主入口点。它会根据你的环境自动选择合适的构建版本（开发或生产）。
  </x-card>
  <x-card data-title="react/jsx-runtime" data-icon="lucide:code">
    生产环境的 JSX 运行时。它包含了将 JSX 语法转译为 React 函数调用的函数，且不包含开发环境的警告。
  </x-card>
  <x-card data-title="react/jsx-dev-runtime" data-icon="lucide:terminal">
    开发环境的 JSX 运行时。该版本包含额外的警告和检查，以帮助你在开发过程中调试应用程序。
  </x-card>
  <x-card data-title="react/compiler-runtime" data-icon="lucide:cpu">
    提供 React 编译器所需的运行时辅助函数。这通常由编译器本身自动管理。
  </x-card>
</x-cards>

## 条件导出

React 使用条件导出为不同环境提供正确的代码。这在客户端和服务端环境（如 React Server Components）以及开发和生产模式之间的区别中最为显著。

### 服务端与客户端环境

许多入口点都有一个 `react-server` 条件。这使得打包工具能够解析到专为服务端渲染和 React Server Components 设计的 React 版本，该版本与客户端版本具有不同的功能集。

如需深入了解此主题，请参阅[服务端与客户端环境](./advanced-server-vs-client.md)指南。

### 开发与生产

React 会根据 `process.env.NODE_ENV` 变量自动在开发和生产构建之间切换。入口点文件通常包含如下逻辑：

```javascript
'use strict';

if (process.env.NODE_ENV === 'production') {
  module.exports = require('./cjs/react.production.js');
} else {
  module.exports = require('./cjs/react.development.js');
}
```

当 `NODE_ENV` 设置为 `'production'` 时，你会得到一个经过压缩和优化的 React 版本。在开发环境中，你会得到一个带有实用警告和调试功能的版本。

## 包导出摘要

下表概述了 `react` 包中可用的主要入口点及其预期用例。

| 导出路径 | 描述 | 支持的条件 |
|---|---|---|
| `.` | React 库的主导出。 | `react-server`, `default` |
| `./jsx-runtime` | 生产环境的 JSX 转换运行时。 | `react-server`, `default` |
| `./jsx-dev-runtime` | 开发环境的 JSX 转换运行时。 | `react-server`, `default` |
| `./compiler-runtime` | React 编译器的运行时辅助函数。 | `react-server`, `default` |
| `./package.json` | 暴露包的 `package.json` 文件。 | N/A |
