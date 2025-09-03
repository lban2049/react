# Transitions

在 React 中，transition 是一种管理 UI 更新而无需阻塞用户交互的机制。它允许你将某些状态更新指定为“transition”，这会告知 React，如果发生更紧急的更新（如用户输入），这些更新可以被中断。这确保了即使在复杂的渲染任务期间，应用程序也能保持响应。

这对于可能较慢的更新特别有用，例如筛选大型列表或获取导致重大视觉变化的数据。其核心思想是将紧急更新（例如，在搜索框中输入）与非紧急或过渡性更新（例如，显示搜索结果）分开。

React 提供了两个用于处理 transition 的主要 API：
- **`useTransition`**：在函数组件中管理 transition 的首选 hook。你可以在我们的 [性能 Hooks](./hooks-performance.md) 页面上找到详细文档。
- **`startTransition`**：一个独立的函数，可在 `useTransition` hook 不可用时使用，例如在数据库或组件外部。

## `startTransition`

`startTransition` 函数允许你包装一个状态更新，将其标记为非紧急。

```javascript
import { startTransition } from 'react';

// startTransition 回调中的任何状态更新都被视为非紧急更新。
startTransition(() => {
  // 例如，为渲染缓慢的组件设置状态
  setFilteredList(data);
});
```

### 工作原理

此图说明了使用 transition 的并发更新流程：

```d2
direction: down

"用户交互": {
  shape: person
}

"紧急更新": {
  shape: rectangle
  label: "紧急状态更新（例如，输入值）"
  style.fill: "#e6f7ff"
}

"过渡更新": {
  shape: rectangle
  label: "过渡状态更新（例如，搜索结果）"
  style.fill: "#fffbe6"
}

"即时渲染": {
  shape: rectangle
  label: "即时、不可中断的渲染"
  style.fill: "#f6ffed"
}

"可中断渲染": {
  shape: rectangle
  label: "可中断的渲染"
  style.fill: "#fff0f6"
}

"用户交互" -> "紧急更新"
"用户交互" -> "过渡更新"

"紧急更新" -> "即时渲染": "触发"
"过渡更新" -> "可中断渲染": "触发"

"即时渲染" -> "可中断渲染": "可中断"

```

当一个状态更新被包装在 `startTransition` 中时，React 可以在后台渲染该更新的同时继续处理用户输入。如果用户提供了新的输入，React 可以中断 transition 的渲染，处理新的紧急更新，然后用最新的数据重新开始 transition 的渲染。

### 参数

`startTransition` 接受一个回调函数和一个可选的 options 对象。

| 名称 | 类型 | 描述 | 必需 |
|---|---|---|---|
| `scope` | `() => void` | 一个执行一个或多个状态更新的函数。这些更新将被标记为 transition。 | 是 |
| `options` | `object` | 一个用于配置的可选对象。目前，它仅支持用于调试目的的 `name`。 | 否 |

- **`options.name`**：一个供 React DevTools 使用的字符串，用于帮助识别和追踪 transition。

### 示例

考虑一个筛选大型列表的搜索输入。我们希望输入字段感觉即时，而列表筛选可以延迟。

```javascript
import { startTransition } from 'react';

// 紧急更新：更新输入字段中的文本
setSearchQuery(input);

// 非紧急更新：将列表过滤操作包装在 transition 中
startTransition(() => {
  setFilteredList(getFilteredItems(input));
});
```

在这种情况下，即使用 `getFilteredItems` 是一个缓慢的操作，用户也可以在搜索框中继续输入而不会有任何延迟，因为 `setSearchQuery` 更新被高优先级处理。

## 实验性 Transition API

React 还包含与 transition 相关的实验性 API，这些 API 可能会发生变化。它们旨在用于高级用例和库作者。

<x-cards data-columns="2">
  <x-card data-title="unstable_startGestureTransition" data-icon="lucide:move-3d">
    用于创建与基于手势的交互（如滑动）相关联的 transition。它需要一个 `GestureProvider` 来管理手势的生命周期。
  </x-card>
  <x-card data-title="unstable_addTransitionType" data-icon="lucide:layers">
    允许向正在进行的 transition 添加描述性类型，例如 'fade' 或 'slide'。这是实验性视图过渡支持的一部分，有助于协调动画。
  </x-card>
</x-cards>

这些 API 通过功能标志（`enableGestureTransition`、`enableViewTransition`）启用，目前不适用于一般的应用程序。

## 总结

Transition 是在 React 中构建复杂且高度响应的用户界面的关键功能。通过将紧急更新与非紧急更新分开，你可以防止应用程序在繁重的渲染任务期间变得迟缓。

对于大多数组件级用例，`useTransition` hook 是理想的工具。对于更高级的场景或在组件外部工作，`startTransition` 提供了必要的控制。

要进一步优化组件的性能，请探索其他内置 hook。

<x-card data-title="性能 Hooks" data-icon="lucide:gauge-circle" data-href="/hooks/performance" data-cta="阅读更多">
  了解其他与性能相关的 hook，如 useTransition 和 useDeferredValue，以优化应用程序的渲染。
</x-card>