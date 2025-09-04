# 高级指南

一旦你对 [核心 API](./core-apis.md) 和 [Hooks](./hooks.md) 有了扎实的掌握，就可以开始探索 React 更强大的功能。本节将深入探讨用于构建复杂、高性能应用程序的高级模式和 API。在这里，你将学习如何优化应用程序的性能、处理不同的渲染环境以及优雅地管理复杂的 UI 状态更新。

这些指南专为熟悉 React 基础并希望解决更具挑战性问题的开发者而设计。

<x-cards data-columns="2">
  <x-card data-title="服务器与客户端环境" data-icon="lucide:server-cog" data-href="/advanced/server-vs-client">
    了解 React 服务器和客户端环境之间的关键区别，以及如何为各自环境使用合适的 API 来构建通用应用程序。
  </x-card>
  <x-card data-title="使用 lazy 和 Suspense 进行代码分割" data-icon="lucide:scissors" data-href="/advanced/code-splitting">
    通过将代码分割成更小的块，并使用 `React.lazy` 和 `Suspense` 按需加载组件，从而缩短应用程序的初始加载时间。
  </x-card>
  <x-card data-title="缓存" data-icon="lucide:database" data-href="/advanced/caching">
    探索 React 的内置缓存机制（如 `cache`），对数据请求和计算进行记忆化，以减少服务器和客户端的冗余工作。
  </x-card>
  <x-card data-title="过渡" data-icon="lucide:hourglass" data-href="/advanced/transitions">
    在复杂的屏幕更新期间保持用户界面的响应。使用过渡来标记非紧急更新，以防止其阻塞用户输入。
  </x-card>
  <x-card data-title="实验性 API" data-icon="lucide:flask-conical" data-href="/advanced/experimental">
    抢先了解即将推出的功能。这些 API 尚不稳定，但可以让你一窥 React 的未来并提供反馈机会。
  </x-card>
</x-cards>

每篇指南都提供了深入的解释和实践范例，以帮助你掌握这些概念。选择一个你感兴趣的主题开始学习，或浏览完整的 [API 参考](./api-reference.md) 以获取每个函数和组件的详细文档。