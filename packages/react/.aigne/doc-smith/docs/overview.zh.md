# 概述

React 是一个用于创建用户界面的 JavaScript 库。其主要目的是让开发者能够构建管理自身状态的封装组件，然后将这些组件组合起来，创建复杂且可交互的用户界面。核心包 `react` 仅包含定义组件所必需的功能，通常与 `react-dom`（用于 Web 应用程序）或 `react-native`（用于原生环境）等渲染器配合使用。

## 核心原则

React 的设计围绕几个关键理念，这些理念使得 UI 开发变得可预测且高效。

<x-cards data-columns="3">
  <x-card data-title="声明式" data-icon="lucide:glasses">
    描述在任何给定状态下你的 UI 应该是什么样子，当数据发生变化时，React 将负责管理更新。
  </x-card>
  <x-card data-title="组件化" data-icon="lucide:blocks">
    从称为“组件”的、小而独立的代码片段构建 UI。这使得你的代码更具可重用性，也更易于理解。
  </x-card>
  <x-card data-title="平台无关性" data-icon="lucide:globe">
    核心的 React 逻辑可以与不同的渲染器配合使用，让你能够在 Web、移动端和其他环境之间共享代码。
  </x-card>
</x-cards>

### React 生态系统

`react` 包提供了定义组件的核心 API，而渲染器则负责处理显示组件的平台特定逻辑。这种分离使得 React 能够适应新的平台。

```d2
direction: down

"react": {
  label: "React 核心\n(定义组件和状态)"
  shape: package
}

renderers: {
  label: "渲染器"
  "react-dom": {
    label: "React DOM\n(渲染至浏览器)"
    shape: package
  }
  "react-native": {
    label: "React Native\n(渲染至原生平台)"
    shape: package
  }
}

platforms: {
  label: "平台"
  "Web 应用程序 (浏览器)"
  "移动应用程序 (iOS/Android)"
}

"react" -> renderers: "供...使用"
renderers."react-dom" -> platforms."Web 应用程序 (浏览器)": 渲染
renderers."react-native" -> platforms."移动应用程序 (iOS/Android)": 渲染

```

**关于环境模式的说明**

默认情况下，React 在开发模式下运行，该模式包含针对常见错误的额外警告。部署应用程序时，使用[生产版本](https://reactjs.org/docs/optimizing-performance.html#use-the-production-build)非常重要，因为它包含了性能优化并移除了仅在开发模式下显示的消息。

## 本文档的结构

本文档的组织结构旨在帮助你高效地查找信息，无论你是刚刚入门还是在探索高级主题。

<x-cards data-columns="2">
  <x-card data-title="入门指南" data-icon="lucide:rocket" data-href="/getting-started">
    一份分步指南，指导你设置新项目并创建你的第一个 React 组件。
  </x-card>
  <x-card data-title="核心 API" data-icon="lucide:puzzle" data-href="/core-apis">
    理解 React 应用程序的基本构建块，从组件和 JSX 到 context 和 refs。
  </x-card>
  <x-card data-title="Hooks" data-icon="lucide:anchor" data-href="/hooks">
    一份关于所有内置 Hooks 的详细指南，它们让你无需编写类即可使用 state 和其他 React 功能。
  </x-card>
  <x-card data-title="高级指南" data-icon="lucide:graduation-cap" data-href="/advanced">
    深入探讨更复杂的 React 功能、模式和环境。
  </x-card>
  <x-card data-title="API 参考" data-icon="lucide:book-open" data-href="/api-reference">
    一份全面的、按字母 A-Z 排序的所有公开 React API 的参考资料，包括组件、hooks 和实用工具。
  </x-card>
  <x-card data-title="包导出" data-icon="lucide:package" data-href="/package-exports">
    关于 'react' 包提供的不同入口点的详细信息，例如 `jsx-runtime` 和 `compiler-runtime`。
  </x-card>
</x-cards>


## 后续步骤

准备好开始构建了吗？最好的起点是[入门指南](./getting-started.md)，它将引导你完成第一个 React 应用程序的创建。