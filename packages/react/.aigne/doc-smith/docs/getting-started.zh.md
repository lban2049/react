# 入门指南

React 是一个用于创建用户界面的 JavaScript 库。本指南将分步介绍如何建立一个新项目并创建你的第一个 React 组件，旨在帮助你尽快上手。

## 你的第一个 React 组件

我们从一个简单且独立的示例开始：一个计数器。该组件将显示一个数字和一个按钮，点击按钮会使数字递增。下面的代码使用了 `react` 和 `react-dom` 这两个包的功能。

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

### 工作原理

1.  **`import` 语句**：我们从 `react` 包中导入用于管理状态的 React Hook `useState`。我们还从 `react-dom/client` 中导入 `createRoot`，该库负责在 Web 浏览器中渲染我们的组件。

2.  **`Counter` 组件**：这是一个函数组件。它是一个 JavaScript 函数，返回对 UI 的描述。在这里，它返回一个标题（`<h1>`）和一个按钮（`<button>`）。

3.  **JSX**：这种类似 HTML 的语法被称为 JSX。它允许你在 JavaScript 代码中以声明方式编写 UI 结构。

4.  **状态管理**：`const [count, setCount] = useState(0);` 是对 `useState` Hook 的调用。它声明了一个名为 `count` 的状态变量，初始值为 `0`，以及一个用于更新其值的函数 `setCount`。点击按钮会调用 `setCount(count + 1)`，这会更新状态并告知 React 使用新的 `count` 值重新渲染组件。

5.  **渲染到 DOM**：最后两行代码会找到 ID 为 `'root'` 的 DOM 元素，并告知 React 在该元素内渲染我们的 `<Counter />` 组件。

## 开发模式与生产模式

默认情况下，React 会处于开发模式。开发版本包含针对常见错误的额外警告，这在开发过程中很有帮助。而生产版本则包含性能优化，并移除了这些警告信息。

部署应用程序时，请务必使用[生产版本](https://reactjs.org/docs/optimizing-performance.html#use-the-production-build)，以确保为用户提供最佳性能。

## 下一步

你已经成功创建了你的第一个 React 组件！这是一个很好的起点。为了继续学习，我们建议你探索以下领域：

<x-cards data-columns="2">
  <x-card data-title="核心 API" data-icon="lucide:cubes" data-href="/core-apis">
    了解 React 应用程序的基本构建块，包括组件、JSX、上下文和 ref。
  </x-card>
  <x-card data-title="Hook" data-icon="lucide:hook" data-href="/hooks">
    深入了解所有内置的 Hook，它们能让你在不编写 class 的情况下使用 state、effect 以及其他 React 功能。
  </x-card>
</x-cards>

如需完整的文档和 API 参考，请访问 React 官方网站 [https://react.dev/](https://react.dev/)。