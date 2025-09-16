# 概述

React 是一个用于构建用户界面的 JavaScript 库。React 的核心思想是，允许你通过名为“组件”的、小巧且独立的可复用代码片段来构建复杂的用户界面。这种基于组件的方法使你能够在每个组件内部管理其状态，从而让代码更具可预测性且易于调试。

`react` 包（当前版本为 **19.1.0**）仅包含定义 React 组件及其状态所需的核心功能。要将这些组件实际渲染到浏览器或原生移动视图中，必须配合渲染器使用。

- **对于 Web 端**，它与 `react-dom` 配合使用。
- **对于原生环境**，它与 `react-native` 配合使用。

这种分离使 React 成为一个不与特定平台绑定的灵活库，让你能够一次学习其原理，并将其应用于各种不同的环境中。

### 核心概念

React 的设计遵循几个核心原则，这些原则使其成为前端开发的强大工具：

<x-cards>
  <x-card data-title="基于组件的架构" data-icon="lucide:layout-template">
    构建管理自身状态的封装组件，然后将它们组合起来，构成复杂的用户界面。组件逻辑使用 JavaScript 编写，而非模板，因此你可以轻松地在应用中传递丰富的数据，并将状态保留在 DOM 之外。
  </x-card>
  <x-card data-title="使用 Hooks 实现状态逻辑" data-icon="lucide:hook">
    Hooks 是一些函数，可以让你从函数组件中“钩入”React 的 state 及生命周期等特性。像 `useState` 和 `useEffect` 这样的 Hooks 允许你在不编写 class 的情况下，为组件添加 state 和副作用。
  </x-card>
  <x-card data-title="平台无关性" data-icon="lucide:box">
    React 核心库的设计旨在独立于任何特定的渲染目标。这使得开发者可以使用相同的组件模型和编程范式来为 Web、移动端乃至虚拟现实进行构建。
  </x-card>
  <x-card data-title="服务器与客户端环境" data-icon="lucide:server">
    React 不再局限于客户端。借助服务器组件等功能，React 可用于构建能够同时利用服务器和客户端环境的应用，以实现最佳性能和用户体验。
  </x-card>
</x-cards>

### 开发模式与生产模式

默认情况下，React 在**开发模式**下运行。此模式会提供有用的警告和错误消息，以帮助在开发过程中发现常见错误。在部署应用程序时，切换到**生产模式**至关重要。生产版本经过高度性能优化，移除了开发警告并对代码进行了压缩。

如需了解更详细的信息，你可以随时查阅 [react.dev](https://react.dev/) 上的官方文档。

### 后续步骤

现在你已经对 React 有了宏观的了解，感受它的最佳方式就是动手构建一些东西。

<x-card data-title="开始使用" data-icon="lucide:rocket" data-href="/getting-started" data-cta="开始构建">
  跟随我们的实践指南，搭建一个简单的 React 应用，并在实践中了解其核心概念。
</x-card>