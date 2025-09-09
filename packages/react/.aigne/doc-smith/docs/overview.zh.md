# 概述

欢迎阅读 `react` 文档，这是一个用于创建用户界面的 JavaScript 库。无论您是构建简单的网页还是复杂的单页应用程序，React 都提供了基于组件的架构来帮助您构建现代化、快速且可扩展的 UI。

`react` 核心包包含了定义 React 组件和管理其状态的基本功能。它几乎总是与渲染器结合使用，例如用于 Web 应用程序的 `react-dom` 或用于移动环境的 `react-native`。

## 核心原则

React 的构建围绕几个核心理念：

*   **基于组件的架构：** 构建管理自身状态的封装组件，然后将它们组合成复杂的 UI。这使您的代码更具可重用性，也更易于理解。
*   **声明式语法：** 只需描述在任何给定状态下您的 UI *应该*是什么样子，当数据发生变化时，React 就会高效地更新和渲染相应的组件。
*   **多样的环境：** 虽然 React 最为人所知的是客户端 Web 开发，但它也可以在服务器上进行渲染，为性能和 SEO 提供了强大的解决方案。

## 如何使用本文档

无论您是初学者还是经验丰富的开发者，本文档的结构都能帮助您快速找到所需内容。

<x-cards data-columns="2">
  <x-card data-title="开始使用" data-icon="lucide:rocket" data-href="/getting-started">
    刚接触 React？从这里开始。我们将引导您设置您的第一个 React 应用程序。
  </x-card>
  <x-card data-title="核心 API" data-icon="lucide:cubes" data-href="/core-apis">
    深入了解 React 的基本构建块，包括组件、JSX、上下文和 refs。
  </x-card>
  <x-card data-title="Hooks" data-icon="lucide:anchor" data-href="/hooks">
    了解 Hooks，这些函数能让您从函数组件中“挂钩”到 React 的状态和生命周期特性。
  </x-card>
  <x-card data-title="高级指南" data-icon="lucide:graduation-cap" data-href="/advanced">
    探索更复杂的主题，如服务器环境、代码分割、性能优化和实验性功能。
  </x-card>
</x-cards>

## 初探

下面是一个简单的 React 组件示例。它是一个独立的计数器，使用 `useState` Hook 来管理其状态。

```javascript 一个简单的计数器组件 icon=logos:react
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

## 开发模式与生产模式

默认情况下，React 在开发模式下运行，该模式包含有用的警告和调试工具。当您准备将应用程序部署给用户时，切换到生产模式至关重要。

生产版本针对性能进行了高度优化，并移除了所有仅限开发环境的警告。部署应用程序时，请务必使用[生产版本](https://reactjs.org/docs/optimizing-performance.html#use-the-production-build)。

---

准备好动手实践了吗？让我们继续阅读[开始使用](./getting-started.md)指南来设置您的开发环境。