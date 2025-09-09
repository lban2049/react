# 过渡

在 React 中，过渡 (Transition) 是一项核心功能，即使在处理复杂且可能缓慢的 UI 更新时，也能保持应用的响应性。通过将某些更新标记为“过渡”，你等于在告诉 React 它们并不紧急，如果此时有更重要的更新（例如用户输入），可以中断这些过渡。这可以防止主线程被阻塞，从而避免卡顿的用户体验。

React 提供了两个用于处理过渡的主要 API：在组件内部使用的 `useTransition` Hook，以及在组件外部使用的 `startTransition` 函数。

若需以组件为中心的指南，你也可以参考 [性能 Hooks](./hooks-performance.md) 文档。

## `useTransition` Hook

`useTransition` hook 是在函数组件中处理过渡最常见的方式。它提供一个有状态的值来跟踪过渡的挂起状态，并提供一个函数来启动它。

它返回一个包含两个元素的数组：
1. `isPending`：一个布尔值，当过渡处于活动状态时为 `true`。你可以用它来显示加载指示器或向用户提供视觉反馈。
2. `startTransition`：一个接收回调函数的函数。你将耗时较长的状态更新包裹在此回调函数中，以将其标记为过渡。

### 示例：筛选大型列表

假设你有一个输入框，用于筛选一个非常长的项目列表。若不使用过渡，每次按键都会触发列表的高开销重新渲染，导致输入时感觉卡顿。而使用 `useTransition`，输入框可以保持响应，同时列表在后台进行更新。

```javascript Filterable List with Transition icon=logos:react
import { useState, useTransition } from 'react';

// 为演示目的生成一个大型列表
const allItems = Array.from({ length: 10000 }, (_, i) => `Item #${i + 1}`);

function MyComponent() {
  const [isPending, startTransition] = useTransition();
  const [inputValue, setInputValue] = useState('');
  const [filteredItems, setFilteredItems] = useState(allItems);

  const handleChange = (e) => {
    // 立即更新输入框状态 - 这是一个紧急更新
    setInputValue(e.target.value);

    // 将高开销的筛选逻辑包裹在 startTransition 中
    startTransition(() => {
      const filtered = allItems.filter((item) =>
        item.toLowerCase().includes(e.target.value.toLowerCase())
      );
      setFilteredItems(filtered);
    });
  };

  return (
    <div>
      <input
        type="text"
        value={inputValue}
        onChange={handleChange}
        placeholder="Search through 10,000 items..."
      />
      {isPending && <p>Updating list...</p>}
      <ul style={{ opacity: isPending ? 0.5 : 1 }}>
        {filteredItems.map((item) => (
          <li key={item}>{item}</li>
        ))}
      </ul>
    </div>
  );
}
```

在此示例中，`setInputValue` 更新被视为紧急更新并立即执行，因此用户可以无延迟地看到自己的输入。`startTransition` 调用包裹了高开销的筛选逻辑和 `setFilteredItems` 更新。如果用户输入了另一个字符，React 现在可以中断此次渲染，从而确保应用体验的流畅性。

## `startTransition` 函数

有时，你需要在组件外部触发非紧急更新，例如在数据请求库、外部 store 或复杂的事件处理程序中。在这些情况下，你可以导入并使用独立的 `startTransition` 函数。

它的工作方式与 `useTransition` hook 返回的函数类似，即包裹一个包含状态更新的回调函数。

### 示例：从数据源更新

```javascript Standalone startTransition icon=logos:javascript
import { startTransition } from 'react';

// 假设此函数是数据请求库的一部分
// 并且 `updateReactState` 是一个调用 React state setter 的函数。
function fetchAndUpdate(data) {
  // 状态更新可能会导致 UI 大规模重新渲染。
  // 我们将其包裹在 startTransition 中以防止阻塞。
  startTransition(() => {
    updateReactState(data);
  });
}
```

这使你可以为任何来源的状态更新利用过渡，从而保持 UI 架构的灵活性和高性能。

## 过渡的工作原理

在底层，过渡允许 React 并发处理多个状态更新。当一个更新被包裹在过渡中时，React 就知道可以暂停它来处理更紧急的任务，并在稍后恢复。该机制是 React 并发渲染能力的核心。

```d2 Transition Interruption Flow
direction: down

User-Input: {
  shape: c4-person
  label: "用户输入\n(例如，打字)"
}

React-Scheduler: {
  label: "React 调度器"
  shape: diamond
}

Urgent-Update: {
  label: "紧急状态更新\n(例如，输入框)"
}

Transition-Update: {
  label: "过渡更新\n(例如，筛选列表)"
}

UI-Render: {
  label: "UI 渲染"
}

User-Input -> React-Scheduler: "触发更新"
React-Scheduler -> Urgent-Update: "优先处理紧急任务"
React-Scheduler -- "中断" --> Transition-Update
Urgent-Update -> UI-Render: "立即渲染"
Transition-Update -> UI-Render: "空闲时渲染"
```

## 实验性过渡 API

尽管 `useTransition` 和 `startTransition` 是稳定功能，但 React 团队仍在探索更高级的过渡能力。以下 API 被视为实验性功能，在生产环境中应谨慎使用。

*   `unstable_startGestureTransition`：专为与用户手势（如拖动或滑动）相关的更平滑的 UI 更新而设计。它需要 `GestureProvider` 才能运行。
*   `unstable_addTransitionType`：作为实验性视图过渡 (View Transitions) 功能的一部分，此函数允许你将特定类型（一个字符串）与正在运行的过渡关联起来，从而实现对动画和行为更精细的控制。

---

过渡是在 React 中构建复杂、高响应性用户界面的强大工具。通过区分紧急和非紧急更新，即使在数据密集型应用中，你也能为用户确保流畅的体验。

### 后续步骤

探索以下相关概念，以进一步提升你的应用性能和用户体验。

<x-cards>
  <x-card data-title="useDeferredValue" data-icon="lucide:arrow-down-right" data-href="/hooks/performance">
    了解一个相关的 hook，它有助于延迟渲染 UI 的非紧急部分，这对于来自 props 或其他 hook 的值特别有用。
  </x-card>
  <x-card data-title="使用 lazy 和 Suspense 进行代码分割" data-icon="lucide:box-select" data-href="/advanced/code-splitting">
    了解如何通过代码分割和使用 Suspense 处理加载状态来改善初始加载时间，这与过渡功能无缝协作。
  </x-card>
</x-cards>