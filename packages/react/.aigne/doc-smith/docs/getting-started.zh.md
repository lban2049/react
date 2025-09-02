# 入门

本节将指导您完成设置基本 React 项目的初始步骤。您将学习如何安装 React 及其渲染器，然后运行一个最小示例，以便快速开始您的客户端开发之旅。

有关 React 的目的及其不同运行时的概述，请参阅[概述](./overview.md)部分。

## 安装

React 通常与针对特定环境的渲染器一起使用。对于 Web 开发，您通常会使用 `react` 包以及 `react-dom`。`react` 包提供定义 React 组件的核心功能，而 `react-dom` 则负责将这些组件渲染到浏览器的文档对象模型 (DOM) 中。

要启动一个新的 React 项目，您需要安装 Node.js 和 npm（Node 包管理器）或 Yarn。安装完成后，您可以安装必要的包：

```bash
npm install react react-dom
# or
yarn add react react-dom
```

## 最小示例

安装 `react` 和 `react-dom` 后，您可以创建您的第一个 React 组件并将其渲染到 DOM。以下是一个最小示例，演示了如何使用 React 的 `useState` Hook 和 `createRoot` 进行渲染，构建一个简单的计数器应用程序。

```js
import { useState } from 'react';
import { createRoot } from 'react-dom/client';

function Counter() {
  const [count, setCount] = useState(0);
  return (
    <>
      <h1>{count}</h1>
      <button onClick={() => setCount(count + 1)}>
        Increment
      </button>
    </>
  );
}

const root = createRoot(document.getElementById('root'));
root.render(<Counter />);
```

此代码定义了一个 `Counter` 函数组件，它使用 `useState` Hook 来管理 `count` 状态。该组件显示当前计数和一个用于递增计数的按钮。`react-dom/client` 中的 `createRoot` 函数初始化一个 React 根，然后使用它将 `Counter` 组件渲染到 ID 为 `root` 的 HTML 元素中。

## 开发版与生产版

默认情况下，React 在开发模式下运行。此版本包含针对常见错误的额外警告，这在开发过程中非常有用。部署应用程序时，使用生产版本至关重要。生产版本包含额外的性能优化，并去除了所有仅限开发阶段的错误消息，从而实现更小的包大小和更快的执行速度。

您的构建工具（例如 Webpack 或 Vite）通常会根据您的环境变量（例如 `process.env.NODE_ENV`）自动配置此项。请确保您的部署过程正确地将环境设置为生产模式，以利用这些优化。

---

在安装 React 并运行一个基本示例后，您现在可以深入了解 React 的基本构建块。请继续阅读[核心概念](./core-concepts.md)部分，了解组件、JSX、状态、属性和元素类型。