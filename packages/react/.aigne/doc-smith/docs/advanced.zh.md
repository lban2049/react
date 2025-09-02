# 高级指南

掌握 React 的核心概念后，你可以探索其更高级的功能来优化应用程序并应对复杂的挑战。本节深入探讨了性能优化、管理不同渲染环境以及利用 React 的并发功能来构建高响应性和可扩展的用户界面等主题。

这些指南专为已熟悉 React 基础知识并希望加深对其架构和功能理解的开发者而设计。

```d2
direction: down

"现代 React 应用": {
  "服务器环境": {
    "数据获取与缓存": {
      shape: hexagon
      "cache()": "记忆化数据请求"
    }
  }

  "客户端环境": {
    "UI 渲染与交互": {
      grid-columns: 2

      "代码分割": {
        shape: hexagon
        "lazy() & Suspense": "按需加载组件"
      }
      "并发更新": {
        shape: hexagon
        "startTransition()": "在更新期间保持 UI 响应"
      }
    }
  }
}

"现代 React 应用"."服务器环境" -> "现代 React 应用"."客户端环境": "提供初始 HTML 和数据" {
  style.animated: true
}
```

探索以下指南，了解有关特定高级主题的更多信息：

<x-cards data-columns="2">
  <x-card data-title="服务器环境 vs. 客户端环境" data-icon="lucide:server-cog" data-href="/advanced/server-vs-client">
    了解在服务器端和客户端渲染的根本区别，并学习在每种环境中可用的特定 API。
  </x-card>
  <x-card data-title="使用 lazy 和 Suspense 进行代码分割" data-icon="lucide:split" data-href="/advanced/code-splitting">
    通过将代码分割成更小的块，并使用 `React.lazy` 和 `Suspense` 按需加载组件，来改善应用的初始加载时间。
  </x-card>
  <x-card data-title="缓存" data-icon="lucide:database" data-href="/advanced/caching">
    探索 React 内置的 `cache` 缓存功能，以记忆化数据请求和计算，从而提升服务器端和客户端的性能。
  </x-card>
  <x-card data-title="过渡" data-icon="lucide:fast-forward" data-href="/advanced/transitions">
    学习如何使用 `startTransition` 来管理复杂且可能缓慢的 UI 更新，而不会阻塞用户交互，从而确保流畅且响应迅速的体验。
  </x-card>
  <x-card data-title="实验性 API" data-icon="lucide:flask-conical" data-href="/advanced/experimental">
    通过探索仍在开发中的实验性功能和 API，一窥 React 的未来。
  </x-card>
</x-cards>

掌握这些高级指南后，你将有能力构建复杂、高性能的 React 应用程序。当你需要查找特定函数或组件时，[API 参考](./api-reference.md) 是你的综合资源。