# 快速入门

本指南旨在通过一个简单的 React 应用设置，提供一份实用的入门介绍。我们将通过一个基础的代码示例，演示如何定义组件并将其渲染至 DOM，帮助你快速上手。

## 基本用法示例

React 允许你使用称为组件的独立部分来构建用户界面。以下是一个简单的 `Counter` 组件示例，该组件会显示一个数字和一个用于递增该数字的按钮。

```javascript 一个简单的计数器组件 icon=logos:javascript
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

### 工作原理

1.  **`import`**：我们导入了必要的函数。`useState` 是一个用于向组件添加状态的 React Hook，而 `createRoot` 来自 `react-dom`，用于将组件渲染到网页中。
2.  **`Counter` 组件**：这是一个函数组件。它使用 `useState` Hook 创建了一个初始化为 `0` 的 `count` 状态变量。
3.  **渲染逻辑**：该组件返回用于描述用户界面的 JSX。它显示当前的 `count` 和一个按钮。当按钮被点击时，它会调用 `setCount` 来更新状态，这会使 React 使用新值重新渲染该组件。
4.  **挂载应用**：最后，`createRoot` 告知 React 在 HTML 中的何处渲染应用——即在 ID 为 `root` 的元素内部。然后，`root.render()` 会显示 `Counter` 组件。

要使此代码正常工作，你需要一个包含 `id` 为 `root` 的 `<div>` 元素的 HTML 文件。这通常与打包工具和开发服务器结合使用，但其核心概念保持不变。

## 使用生产版本

默认情况下，React 在开发模式下运行，该模式会针对常见错误提供有用的警告。当你准备将应用部署给用户时，使用生产版本至关重要。

生产版本针对性能进行了优化，并移除了开发过程中的警告和错误信息。有关如何进行设置的更多详情，请参阅关于[优化性能](https://reactjs.org/docs/optimizing-performance.html#use-the-production-build)的官方指南。

## 后续步骤

既然你已经看过一个基本示例，接下来可以更深入地探索 React 的功能：

*   **[概述](./overview.md)**：获取关于 React 用途和主要特性的高级介绍。
*   **[API 参考](./api-reference.md)**：探索完整的 React API，获取有关钩子、组件和函数的详细信息。
*   **[官方文档](https://react.dev/)**：访问 React 官方网站，获取全面的教程、指南和社区资源。