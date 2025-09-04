# 概述

React 是一个用于创建用户界面的 JavaScript 库。自 **19.1.0** 版本起，它提供了构建封装组件所需的基础工具，这些组件能够管理自身状态，并通过组合来构建复杂的 UI。

`react` 包本身仅包含定义组件、Hooks 和上下文所必需的功能。它旨在与渲染器（如用于 Web 应用的 `react-dom` 或用于原生移动环境的 `react-native`）配合使用。这种分离使得 React 可以在浏览器之外的多种环境中使用。

```d2
direction: down

"Your Application": {
  shape: rectangle
  label: "你的应用程序代码\n(组件, Hooks, 逻辑)"
}

"React Ecosystem": {
  shape: package
  grid-columns: 2
  grid-gap: 50

  "react-package": {
    label: "`react`\n核心逻辑"
    shape: package
  }

  "react-renderers": {
    label: "渲染器"
    shape: rectangle
    grid-columns: 1

    "react-dom": {
      label: "`react-dom`\n(用于 Web)"
      shape: package
    }
    "react-native": {
      label: "`react-native`\n(用于原生)"
      shape: package
    }
  }
}

"Platform-UI": {
  shape: rectangle
  label: "平台 UI\n(浏览器 DOM 或原生视图)"
}

"Your Application" -> "React Ecosystem"."react-package": "使用"
"React Ecosystem"."react-package" -> "React Ecosystem"."react-renderers": "与之协调"
"React Ecosystem"."react-renderers" -> "Platform-UI": "渲染至"
```

**关于构建的说明：** 默认情况下，React 在开发模式下运行，该模式包含了针对常见错误的实用警告。在部署时，应始终使用为性能而优化的[生产版本](https://reactjs.org/docs/optimizing-performance.html#use-the-production-build)。

## 基本用法示例

以下是一个使用 `useState` Hook 管理计数器的 React 组件的简单示例。

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

## 本文档的结构

无论你是初学者还是经验丰富的开发者，本文档都旨在帮助你学习 React。其内容分为以下几个主要部分：

<x-cards data-columns="2">
  <x-card data-title="入门" data-icon="lucide:rocket" data-href="/getting-started">
    一份分步指南，指导你如何建立新项目并创建你的第一个 React 组件。
  </x-card>
  <x-card data-title="核心 API" data-icon="lucide:cubes" data-href="/core-apis">
    理解 React 的基本构建块，从组件和 JSX 到 context 和 refs。
  </x-card>
  <x-card data-title="Hooks" data-icon="lucide:anchor" data-href="/hooks">
    一份关于所有内置 Hooks 的详细指南，让你无需编写 class 即可使用 state 及其他 React 特性。
  </x-card>
  <x-card data-title="高级指南" data-icon="lucide:graduation-cap" data-href="/advanced">
    深入探讨更复杂的 React 特性、模式和环境，如服务器组件和缓存。
  </x-card>
</x-cards>

## 后续步骤

如需开始构建你的第一个应用程序，请继续阅读[入门](./getting-started.md)指南。