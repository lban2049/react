# API 参考

本节概述了 `react-cache` 公开的公共 API，`react-cache` 是一个专为 React 应用程序中的数据缓存设计的实验性工具。这些 API 允许您定义和管理可缓存资源，并控制全局缓存行为。

请务必记住，`react-cache` 仍处于实验阶段。有关其预发布状态和使用警告的详细信息，请参阅[实验状态和警告](./Experimental-Status-and-Warnings.md)部分。

## 可用 API

### `unstable_createResource`

`unstable_createResource` 函数用于定义可缓存资源。它接受一个数据获取函数和一个可选的键哈希函数，并返回一个 `Resource` 对象。此 `Resource` 对象提供 `read` 和 `preload` 方法以与缓存数据进行交互。

要了解有关如何创建和管理资源（包括处理数据状态和定义自定义键）的更多信息，请访问 [`unstable_createResource`](./API-Reference-unstable_createResource.md) 的详细文档。

### `unstable_setGlobalCacheLimit`

`unstable_setGlobalCacheLimit` 函数允许您调整全局 `react-cache` LRU（最近最少使用）缓存可以容纳的最大条目数。这直接影响了何时将较旧、访问频率较低的条目从缓存中逐出，为新数据腾出空间。

有关设置和理解全局缓存限制影响的全面指南，请参阅 [`unstable_setGlobalCacheLimit`](./API-Reference-unstable_setGlobalCacheLimit.md) 的文档。

---

本节介绍了 `react-cache` 的主要 API。要深入了解这些 API 的构建方式及其底层机制，您可能会发现[核心概念](./Core-Concepts.md)文档很有帮助。强烈建议在使用此包之前查阅[实验状态和警告](./Experimental-Status-and-Warnings.md)。