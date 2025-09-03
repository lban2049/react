# 高级指南

一旦你对 React 的核心概念有了扎实的掌握，这些高级指南将帮助你应对在构建现代化、高性能应用程序时遇到的更复杂的挑战。本节将探讨用于优化性能、管理复杂状态更新以及理解 React 在不同环境中行为的强大功能。

这些主题专为希望加深对 React 功能的理解并构建更复杂应用程序的开发者而设计。

<x-cards data-columns="2">
  <x-card data-title="服务端与客户端环境" data-icon="lucide:server-cog" data-href="/advanced/server-vs-client">
    理解在服务端和客户端渲染 React 之间的主要区别，并了解在每种环境中可用的特定 API。
  </x-card>
  <x-card data-title="使用 lazy 和 Suspense 进行代码分割" data-icon="lucide:scissors" data-href="/advanced/code-splitting">
    通过将代码分割成更小的块，并使用 React.lazy 和 Suspense 按需加载组件，来缩短应用程序的初始加载时间。
  </x-card>
  <x-card data-title="缓存" data-icon="lucide:database" data-href="/advanced/caching">
    探索 React 的内置缓存功能，以记忆化函数结果并优化数据获取，从而提升性能和用户体验。
  </x-card>
  <x-card data-title="过渡" data-icon="lucide:fast-forward" data-href="/advanced/transitions">
    学习如何使用过渡来在复杂的屏幕更新期间保持用户界面的响应性，防止渲染阻塞，以获得更流畅的体验。
  </x-card>
  <x-card data-title="实验性 API" data-icon="lucide:flask-conical" data-href="/advanced/experimental">
    通过探索实验性 API 来一窥 React 的未来。这些功能仍在开发中，并为早期反馈提供了机会。
  </x-card>
</x-cards>

## 高级功能如何协同工作

许多这些高级功能被设计为协同工作。例如，一次用户交互可以触发一个过渡，该过渡涉及懒加载一个新组件，而这个组件又会使用一个缓存的函数来获取其数据，所有这些都由 Suspense 无缝地协调。

```d2
direction: down

"用户交互": {
  shape: circle
}

"startTransition()": {
  shape: rectangle
}

"Suspense 边界": {
  shape: package
  grid-columns: 1

  "UI 显示后备内容": {
    shape: rectangle
    style.stroke-dash: 2
  }

  "懒加载组件": {
    shape: rectangle
  }

  "cache() function": {
    shape: rectangle
  }

  "懒加载组件" -> "cache() function": "获取数据"
}

"用户交互" -> "startTransition()": "包装状态更新"
"startTransition()" -> "Suspense 边界": "触发渲染"
"Suspense 边界"."懒加载组件" -> "UI 显示后备内容": "挂起"
```

## 后续步骤

这些指南为你提供了构建复杂且高度优化的 React 应用程序所需的工具。在探索了这些主题之后，你可能会发现查阅完整的 [API 参考](./api-reference.md) 会很有帮助，以获取有关特定函数和钩子的深入细节。