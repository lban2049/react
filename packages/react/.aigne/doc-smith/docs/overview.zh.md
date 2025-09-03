# 概述

React 是一个用于创建用户界面的 JavaScript 库。它允许你构建能够管理自身状态、可封装、可复用的组件，然后将这些组件组合成复杂的用户界面。它通常与 `react-dom`（用于 Web）或 `react-native`（用于原生环境）等渲染器一起使用。

本文档旨在简要介绍 React 的核心原则，并说明此文档的结构。

## 核心概念

要理解 React，熟悉几个关键概念会很有帮助。

### 组件和 JSX

React 应用由被称为组件的独立 UI 片段构建而成。组件是一个返回部分 UI 的 JavaScript 函数。下面是一个简单的 `Counter` 组件示例：

```javascript
import { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);
  return (
    <>
      <h1>{count}</h1>
      <button onClick={() => setCount(count + 1)}>
        递增
      </button>
    </>
  );
}
```

函数内部的类 HTML 语法被称为 JSX。它是 JavaScript 的一种语法扩展，允许你直接在代码中编写 UI 描述。

### State 和 Hook

组件可以使用“state”来管理内部数据。当组件的 state 发生变化时，React 会高效地更新并重新渲染该组件以反映新信息。在上面的示例中，`useState` 就是一个 **Hook**。Hook 是一些函数，可以让你从函数组件中“钩入”React 的 state 和生命周期功能，从而提供了一种直接而强大的方式来管理组件的行为。

### 客户端和服务器环境

`react` 包仅包含定义组件所必需的功能。渲染器会使用这个核心库将你的组件转换为特定平台上的用户界面。这种分离使得 React 可以在不同环境中运行，包括客户端（在 Web 浏览器中）和服务器端。

```d2
direction: down

"你的应用程序" {
  shape: rectangle
}

"React 生态系统" {
  shape: package
  grid-columns: 1
  grid-gap: 50

  "React 核心 (`react` 包)": {
    shape: hexagon
    "定义组件、State、Hook"
  }

  "渲染器": {
    shape: rectangle
    grid-columns: 2
    
    "react-dom": {
      label: "Web (react-dom)"
      "渲染到 DOM"
    }
    
    "react-native": {
      label: "原生 (react-native)"
      "渲染到原生 UI 元素"
    }
  }
}

"用户界面": {
  shape: cloud
}

"你的应用程序" -> "React 生态系统"."React 核心 (`react` 包)": "使用"
"React 生态系统"."React 核心 (`react` 包)" -> "React 生态系统".渲染器: "驱动"
"React 生态系统".渲染器 -> "用户界面": "创建"
```

## 本文档的结构

无论你是要构建第一个组件，还是要优化一个大型应用，本文档都能为你提供帮助。其内容分为以下几个关键部分：

<x-cards data-columns="2">
  <x-card data-title="入门" data-icon="lucide:play-circle" data-href="/getting-started">
    设置你的开发环境，并构建你的第一个 React 组件。
  </x-card>
  <x-card data-title="核心 API" data-icon="lucide:cuboids" data-href="/core-apis">
    学习基础构建模块，包括组件、JSX、prop 和 context。
  </x-card>
  <x-card data-title="Hook" data-icon="lucide:hook" data-href="/hooks">
    一份关于所有内置 Hook 的完整指南，用于管理 state、副作用和性能。
  </x-card>
  <x-card data-title="高级指南" data-icon="lucide:graduation-cap" data-href="/advanced">
    探索代码分割、服务器环境、缓存和过渡等主题。
  </x-card>
</x-cards>

## 后续步骤

如果你已准备好开始构建，最好的起点是 [入门](./getting-started.md) 指南。该指南将引导你从零开始创建一个简单的 React 应用。
