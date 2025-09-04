# 核心 API

任何 React 应用的核心都是其核心 API。它们是用于定义、组织和管理用户界面的基本构建模块。理解这些概念是构建稳健且易于维护的应用的关键。本节将概述这些基础部分，从使用 JSX 创建组件，到使用 Context 管理数据流，再到使用 Refs 直接与 DOM 交互。

这些 API 协同工作，将组件逻辑转换为高效的 UI 表示，然后由 React 渲染到浏览器中。

```d2
direction: down

"JSX": {
  label: "JSX 语法\n(例如 <div />)"
  shape: rectangle
}

"createElement": {
  label: "React.createElement()"
  shape: rectangle
}

"React-Element": {
  label: "React 元素\n(虚拟 DOM 节点)"
  shape: rectangle
}

"Component": {
  label: "组件\n(类或函数)"
  shape: class
  
  "Props": { shape: parallelogram }
  "State": { shape: parallelogram }
}

"DOM": {
  label: "浏览器 DOM"
  shape: cylinder
}

"Context": {
  label: "Context"
  shape: package
}

"Ref": {
  label: "Ref"
  shape: rectangle
}

"JSX" -> "createElement": "编译为"
"createElement" -> "React-Element": "创建"
"Component" -> "React-Element": "渲染"
"React-Element" -> "DOM": "协调为"

"Context" -> "Component": "提供数据"
"Ref" -> "DOM": "访问"

```

探索 React 核心 API 的不同构建模块，了解如何有效地组织应用结构。

<x-cards data-columns="2">
  <x-card data-title="组件与 Props" data-href="/core-apis/components-and-props" data-icon="lucide:component">
    组件是 React UI 中可复用、独立的构建模块。学习如何创建类组件和函数组件，以及如何使用 props 将数据沿组件树向下传递。
  </x-card>
  <x-card data-title="JSX" data-href="/core-apis/jsx" data-icon="lucide:code-2">
    探索 JSX，这是一种 JavaScript 的语法扩展，它使描述 UI 结构变得直观易读。了解它如何编译为标准的 `React.createElement` 调用。
  </x-card>
  <x-card data-title="创建与操作元素" data-href="/core-apis/creating-elements" data-icon="lucide:plus-square">
    深入了解 `React.createElement` 和 `cloneElement` 函数。这些 API 提供了不使用 JSX 而直接创建和修改 React 元素的控制能力。
  </x-card>
  <x-card data-title="Refs" data-href="/core-apis/refs" data-icon="lucide:pin">
    学习如何使用 refs 直接访问和操作 DOM 节点或 React 组件实例。本节涵盖了用于类组件的 `createRef` 和用于通过组件暴露 refs 的 `forwardRef`。
  </x-card>
  <x-card data-title="Context" data-href="/core-apis/context" data-icon="lucide:share-2">
    通过使用 Context 在组件树中传递数据来管理全局状态或避免“属性逐层传递（prop drilling）”。了解如何使用 `createContext` 与任何后代组件共享数据。
  </x-card>
  <x-card data-title="Children 工具集" data-href="/core-apis/children-utilities" data-icon="lucide:list-tree">
    有效地处理不透明的 `props.children` 数据结构。`React.Children` 工具集提供了如 `map`、`forEach` 和 `toArray` 等安全的方法来操作 children。
  </x-card>
</x-cards>

### 后续步骤

掌握这些核心 API 能为使用 React 构建任何应用奠定坚实的基础。一旦你熟悉了这些概念，就可以开始探索 Hooks 如何简化函数组件中的状态管理和副作用处理。

[下一步：Hooks](./hooks.md)
