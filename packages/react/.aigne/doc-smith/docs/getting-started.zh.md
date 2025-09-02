# 快速入门

本指南将引导你创建第一个 React 组件。旨在通过实际操作，让你初步了解 React 的核心概念。

React 是一个用于创建用户界面的 JavaScript 库。`react` 包提供了定义组件所需的函数，而像 `react-dom` 这样的渲染器则用于将组件渲染到网页上。

## 你的第一个 React 组件

我们来创建一个简单的计数器组件。这个例子展示了 React 的两个基本概念：组件和状态。

以下是一个基本交互式计数器的完整代码：

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

### 代码解析

我们来逐步分析这个例子，了解其工作原理。

#### 1. 从 React 和 ReactDOM 导入

```javascript
import { useState } from 'react';
import { createRoot } from 'react-dom/client';
```

- `useState`：这是一个 **Hook**。Hook 让你能够在函数组件中使用 state 以及其他 React 特性。`useState` 专门用于为组件添加 state。
- `createRoot`：这个函数来自 `react-dom/client` 包，用于设置 React 应用的根节点，并指定 UI 在 DOM 中的渲染位置。

#### 2. 定义组件

```javascript
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
```

- `function Counter() { ... }`：这是一个 React 组件。它是一个 JavaScript 函数，返回对 UI 的描述，该描述使用一种名为 JSX 的语法编写，类似于 HTML。
- `const [count, setCount] = useState(0);`：这里我们调用 `useState` Hook 为组件添加 state。`count` 是 state 变量，用于保存当前的计数值（初始化为 `0`）。`setCount` 是一个更新此 state 的函数。
- `return (...)`：组件返回定义其输出的 JSX。它在一个 `<h1>` 标签和一个按钮中显示当前的 `count`。当按钮被点击时（`onClick`），它会调用 `setCount` 函数来增加 `count` state 变量的值。

#### 3. 渲染组件

```javascript
const root = createRoot(document.getElementById('root'));
root.render(<Counter />);
```

- `createRoot(document.getElementById('root'))`：这告诉 React 接管 ID 为 `root` 的 DOM 元素。
- `root.render(<Counter />)`：这将 `Counter` 组件渲染到由 `root` 管理的 DOM 元素中。

## 重要提示：开发模式与生产模式

默认情况下，React 在开发模式下运行。该模式包含针对常见错误的实用警告。部署应用时，使用生产构建至关重要，因为它针对性能进行了优化并移除了这些警告。

更多详情，请参阅[使用生产构建](https://reactjs.org/docs/optimizing-performance.html#use-the-production-build)。

## 后续步骤

你已经成功构建了第一个 React 组件！为了继续学习，我们建议你探索 React 应用的核心构建块。

<x-cards>
  <x-card data-title="核心 API" data-icon="lucide:cuboid" data-href="/core-apis">
    了解组件、JSX、context 和 ref 等基本概念。
  </x-card>
  <x-card data-title="Hook" data-icon="lucide:anchor" data-href="/hooks">
    深入了解所有内置 Hook，它们可以为你的组件添加 state 和其他功能。
  </x-card>
</x-cards>