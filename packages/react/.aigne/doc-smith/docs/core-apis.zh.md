# 核心 API

欢迎来到 React 的核心。核心 API 是用于创建丰富、交互式用户界面的基本构建块。它们提供了定义组件、使用 JSX 描述其结构以及管理数据流的基本工具。本节概述了这些基础概念。请深入了解每个主题，以获得全面的理解。

<x-cards data-columns="2">
  <x-card data-title="组件和 Props" data-icon="lucide:component" data-href="/core-apis/components-and-props">
    组件是可重用、自包含的用户界面（UI）片段。学习如何将它们构建为函数或类，并通过 props 向其传递数据。
  </x-card>
  <x-card data-title="JSX" data-icon="lucide:braces" data-href="/core-apis/jsx">
    JSX 是一种语法扩展，它允许你在 JavaScript 代码中编写类似 HTML 的标记，从而使 UI 逻辑更直观、更易于阅读。
  </x-card>
  <x-card data-title="创建和操作元素" data-icon="lucide:plus-square" data-href="/core-apis/creating-elements">
    学习超越 JSX，直接使用 `createElement` 和 `cloneElement` 等函数来处理 React 元素，以实现动态 UI 生成。
  </x-card>
  <x-card data-title="Refs" data-icon="lucide:anchor" data-href="/core-apis/refs">
    直接访问 DOM 节点或 React 组件实例并与之交互。Refs 提供了一种应急方案，可用于管理焦点、动画或与第三方库集成。
  </x-card>
  <x-card data-title="Context" data-icon="lucide:share-2" data-href="/core-apis/context">
    无需在每个层级手动传递 props，即可通过组件树传递数据，从而避免“prop 逐层传递（prop drilling）”的问题。非常适合用于处理主题或用户认证等全局数据。
  </x-card>
  <x-card data-title="Children 工具集" data-icon="lucide:blocks" data-href="/core-apis/children-utilities">
    `props.children` 属性可能是一个复杂的数据结构。`React.Children` 工具集提供了安全的方法来映射、计数和遍历 children。
  </x-card>
</x-cards>

## 后续步骤

掌握这些核心 API 是精通 React 的第一步。一旦熟悉了这些概念，你就可以开始探索一种更现代、更强大的组件构建方式。接下来，顺理成章的步骤就是深入学习 Hooks。

继续阅读 [Hooks](./hooks.md)，学习如何在不编写类的情况下使用 state 及其他 React 特性。