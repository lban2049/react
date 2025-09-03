# 快速入门

React 是一个用于创建用户界面的 JavaScript 库。本指南将分步介绍如何设置新项目并创建你的第一个 React 组件，旨在帮助你快速地从零开始，构建一个可运行的示例。

## 创建你的第一个组件

在 React 中，最基本的构建单元是**组件**。组件是一个独立的、可复用的 UI 片段。以下是一个简单的 `Counter` 组件的完整示例。

```javascript
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

让我们分解一下上面的代码：

1.  **导入**：我们从 `react` 包导入 `useState`，从 `react-dom/client` 导入 `createRoot`。`react` 包含定义组件的核心功能，而 `react-dom` 是与 Web 浏览器的 DOM 交互的渲染器。

2.  **组件定义**：`Counter` 是一个函数组件。它是一个返回 UI 描述的 JavaScript 函数。这个描述是用 JSX 编写的，JSX 是一种看起来与 HTML 相似的语法扩展。

3.  **状态管理**：`const [count, setCount] = useState(0);` 是对 React **Hook** 的调用。`useState` 允许函数组件持有自己的状态。它返回当前的状态值 (`count`) 和一个更新它的函数 (`setCount`)。我们将 `count` 初始化为 `0`。

4.  **UI 和事件处理**：该组件返回一个用于显示当前 `count` 的 `h1` 元素和一个 `button`。该按钮有一个 `onClick` 事件处理程序，当触发时，会调用 `setCount` 将状态加一。当状态改变时，React 会自动重新渲染组件以反映新的 UI。

5.  **渲染到 DOM**：最后几行将我们的 React 组件连接到浏览器。`createRoot` 在一个 DOM 元素上建立一个 React 根（假设你的 HTML 文件中有 `<div id="root"></div>`），而 `root.render(<Counter />);` 告诉 React 在其中渲染我们的 `Counter` 组件。

## 开发模式与生产模式

默认情况下，React 将处于开发模式。开发版本包含有关常见错误的额外警告。而生产版本则包含性能优化，并移除了这些警告信息。

在部署你的应用程序时，使用生产构建至关重要，以确保为你的用户提供最佳性能。你可以在官方文档中找到有关如何[使用生产构建](https://reactjs.org/docs/optimizing-performance.html#use-the-production-build)的更多信息。

## 接下来做什么？

你已经成功构建了你的第一个 React 组件。要继续你的学习之旅并构建更复杂的应用程序，我们建议你探索以下主题：

<x-cards data-columns="2">
  <x-card data-title="Core APIs" data-icon="lucide:cuboid" data-href="/core-apis">
    了解 React 的基本构建单元，包括组件、Props、JSX 和 Context。
  </x-card>
  <x-card data-title="Hooks" data-icon="lucide:anchor" data-href="/hooks">
    探索所有内置的 Hook，它们让你无需编写类即可使用 state 和其他 React 功能。
  </x-card>
</x-cards>