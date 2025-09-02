# 核心 API

欢迎来到 React 的核心。这些是用于创建丰富、交互式用户界面的基本构建块。无论是构建简单的微件还是大型企业平台，理解这些 API 都是构建任何 React 应用的基础。本节将提供一个高层次的概述，并引导您阅读每个概念的详细指南。

概括地说，您使用 JSX 编写 UI 描述，这些描述会被转换为 React 元素。这些元素是组件的输出，而组件通过 props 接收数据。对于更复杂的数据流，Context 允许您在组件树中传递数据，而 Refs 则提供了一种直接与 DOM 节点或组件实例交互的方法。

```d2
direction: down

"JSX" -> "React.createElement()": "编译为" {
  style.animated: true
}

"React.createElement()" -> "React Element": "创建" {
  style.animated: true
}

"Component": {
  shape: hexagon
  "接收 Props"
  "返回 React 元素"
}

"Component" -> "React Element"

"应用树": {
  "Context 提供者"

  "组件 A": {
    "Ref" -> "DOM Node": 访问 {
      style.stroke-dash: 4
    }
  }

  "组件 B"
}

"Context 提供者" -> "组件 B": "提供数据" {
    style.stroke-dash: 4
}
```

通过以下指南深入了解每个核心概念的具体细节。

<x-cards data-columns="2">
  <x-card data-title="组件与 Props" data-icon="lucide:layout-template" data-href="/core-apis/components-and-props">
    每个 React 应用的核心。学习如何使用函数组件和类组件构建可复用的 UI 片段，并通过 props 传递数据。
  </x-card>
  <x-card data-title="JSX" data-icon="lucide:code" data-href="/core-apis/jsx">
    探索 JavaScript 的语法扩展，它允许您编写类似 HTML 的 UI 描述。了解它如何编译为常规的 JavaScript 函数调用。
  </x-card>
  <x-card data-title="创建和操作元素" data-icon="lucide:box-select" data-href="/core-apis/creating-elements">
    深入了解 JSX 的内部机制，学习 `createElement` 和 `cloneElement`。这些工具是直接创建和操作 React 元素的基础。
  </x-card>
  <x-card data-title="Refs" data-icon="lucide:anchor" data-href="/core-apis/refs">
    学习如何创建和使用 refs 来直接访问 DOM 元素或 React 组件实例，这对于管理焦点、媒体播放或集成第三方库至关重要。
  </x-card>
  <x-card data-title="Context" data-icon="lucide:share-2" data-href="/core-apis/context">
    通过在组件树中传递数据来管理全局状态或避免“属性逐层传递（prop drilling）”，无需在每个层级手动传递 props。
  </x-card>
  <x-card data-title="Children 工具" data-icon="lucide:blocks" data-href="/core-apis/children-utilities">
    探索 `React.Children` 工具，它提供了 `map`、`forEach` 和 `toArray` 等实用方法，用于处理不透明的 `props.children` 数据结构。
  </x-card>
</x-cards>

### 后续步骤

在您熟悉了这些核心 API 之后，就可以探索强大的 Hooks 世界了。Hooks 允许您在函数组件中使用 state、生命周期特性以及其他功能。

➡️ **继续阅读：[Hooks](./hooks.md)**