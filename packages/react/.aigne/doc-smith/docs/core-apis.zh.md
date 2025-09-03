# 核心 API

欢迎来到 React 的核心。核心 API 是创建任何 React 应用程序所使用的基本构建块。它们为定义 UI 组件、管理数据流以及与浏览器 DOM 交互提供了必要的工具。在深入了解 Hooks 等更高级的功能之前，理解这些核心概念对于构建稳健的应用程序至关重要。

概括地说，你使用 JSX 编写 UI 结构，JSX 会被转换为 React 元素。这些元素是你希望在屏幕上看到的轻量级描述。然后，React 会接收这些元素并高效地更新 DOM 以与之匹配，这通常通过封装了逻辑和状态的组件来完成。

```d2
direction: down

"JSX": {
  shape: document
  label: "JSX (UI 语法)"
}

"React.createElement()": {
  shape: rectangle
}

"React Element": {
  shape: rectangle
  label: "React Element (UI 的对象描述)"
}

"Component": {
  shape: package
  label: "Component (函数或类)"
  "Props": { shape: parallelogram }
  "Context": { shape: parallelogram }
  "Ref": { shape: parallelogram }
}

"React.Children": {
  shape: rectangle
}

"DOM": {
  shape: cylinder
  label: "浏览器 DOM"
}

"JSX" -> "React.createElement()": "被转译为"
"React.createElement()" -> "React Element": "创建"
"React Element" -> "Component": "描述一个实例"
"Component" -> "DOM": "渲染到"
"Component"."Props" -> "Component": "接收数据"
"Component" -> "Component"."Context": "可以访问"
"Component" -> "Component"."Ref": "可以访问"
"React.Children" -> "Component"."Props": "操作 props.children"

```

本节为每个基本构建块提供了详细的指南。通过探索这些内容，你可以在 React 开发中打下坚实的基础。

<x-cards data-columns="2">
  <x-card data-title="组件和 Props" data-icon="lucide:component" data-href="/core-apis/components-and-props">
    学习如何使用函数和类组件创建及组合 UI，以及如何使用 props 传递数据。
  </x-card>
  <x-card data-title="JSX" data-icon="lucide:code" data-href="/core-apis/jsx">
    探索如何使用 JSX 语法描述你的 UI，并了解客户端和服务器的不同运行时。
  </x-card>
  <x-card data-title="创建和操作元素" data-icon="lucide:box-select" data-href="/core-apis/creating-elements">
    学习 `createElement`、`cloneElement` 以及其他直接操作 React 元素的实用工具。
  </x-card>
  <x-card data-title="Refs" data-icon="lucide:anchor" data-href="/core-apis/refs">
    使用 `createRef` 和 `forwardRef` 直接访问 DOM 节点或 React 组件并与之交互。
  </x-card>
  <x-card data-title="Context" data-icon="lucide:merge" data-href="/core-apis/context">
    将数据在组件树中传递，而无需在每个层级手动传递 props。
  </x-card>
  <x-card data-title="Children 实用工具" data-icon="lucide:list-tree" data-href="/core-apis/children-utilities">
    使用 `React.Children` API 辅助函数来处理 `props.children` 这个不透明的数据结构。
  </x-card>
</x-cards>


## 后续步骤

掌握这些核心 API 将为构建可扩展的 React 应用程序奠定坚实的基础。一旦你熟悉了这些概念，就可以开始探索 Hooks 如何为函数组件添加状态和其他强大的功能。

继续阅读下一节，了解 [Hooks](./hooks.md)。