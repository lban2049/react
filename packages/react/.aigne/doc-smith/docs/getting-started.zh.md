# 快速入门

欢迎来到 React！本指南将逐步引导你创建第一个 React 组件。React 是一个用于构建用户界面的 JavaScript 库，本介绍旨在帮助你快速上手。

我们将构建一个简单的计数器应用，以展示组件、状态和渲染等基本概念。

### 你的第一个 React 组件

下面是一个简单计数器组件的完整、可运行的示例。我们将在接下来的章节中详细讲解其工作原理。

```javascript 你的第一个 React 组件 icon=logos:react
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

要让这段代码在浏览器中运行，你需要一个 HTML 文件作为应用的入口点。

### 第 1 步：设置 HTML 文件

React 组件会渲染到 HTML 文件中的一个特定 DOM 元素中。创建一个 `index.html` 文件，其中包含一个 `<div>`，它将作为我们应用的根容器。

```html index.html icon=logos:html-5
<!DOCTYPE html>
<html>
  <head>
    <title>我的第一个 React 应用</title>
  </head>
  <body>
    <div id="root"></div>
    <!-- 你的 React 代码将在这里加载 -->
    <script type="module" src="index.js"></script>
  </body>
</html>
```

### 第 2 步：定义和渲染组件

React 的核心是定义可复用的组件。`react` 包提供了创建这些组件所需的函数，而像 `react-dom` 这样的渲染器则用于在浏览器中显示它们。

- **`function Counter() { ... }`**：这是一个 React 组件。它是一个标准的 JavaScript 函数，返回对 UI 的描述，该描述使用一种名为 JSX 的语法编写。
- **`useState(0)`**：这是一个 **Hook**。它允许我们向函数组件添加状态。`useState` 返回一个值对：当前状态（`count`）和一个更新状态的函数（`setCount`）。我们将状态初始化为 `0`。
- **`onClick={() => setCount(count + 1)}`**：这是一个事件处理程序。当按钮被点击时，它会调用 `setCount` 函数，并传入新的状态值（`count + 1`）。然后，React 会使用更新后的 `count` 重新渲染 `Counter` 组件，UI 随之改变以显示新的数字。

### 第 3 步：挂载应用

定义组件后，我们需要告诉 React 将其渲染到我们在 HTML 文件中创建的根 `<div>` 中。

- **`createRoot(document.getElementById('root'))`**：这个来自 `react-dom/client` 的函数为指定的 DOM 容器创建一个 React 根。
- **`root.render(<Counter />)`**：这将我们的 `Counter` 组件渲染到根中，使其在屏幕上可见。

### 开发模式与生产模式

默认情况下，React 在开发模式下运行。该版本在开发过程中很有用，因为它会针对常见错误提供额外的警告。当你准备将应用部署给用户时，使用生产版本至关重要。生产版本针对性能进行了优化，并移除了仅在开发模式下出现的警告。

更多详情，请参阅[使用生产版本](https://reactjs.org/docs/optimizing-performance.html#use-the-production-build)。

### 后续步骤

你已成功创建了第一个 React 组件！现在，你可以深入了解使 React 如此强大的核心概念了。

<x-cards>
  <x-card data-title="核心 API" data-icon="lucide:cuboid" data-href="/core-apis" data-cta="探索核心 API">
    了解 React 的基本构建块，从组件和 JSX 到 context 和 refs。
  </x-card>
  <x-card data-title="Hooks" data-icon="lucide:anchor" data-href="/hooks" data-cta="学习 Hooks">
    一份关于所有内置 Hooks 的详细指南，这些 Hooks 能让你在不编写类的情况下使用 state 和其他功能。
  </x-card>
</x-cards>