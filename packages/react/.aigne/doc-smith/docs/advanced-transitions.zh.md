# 过渡

React 中的过渡功能可以帮助你管理复杂的 UI 更新，而不会阻塞主线程，从而确保应用程序对用户输入保持响应。通过将某些状态更新标记为“过渡”，你可以告知 React 它们并非紧急任务，如果出现更关键的更新（例如按键或点击），这些更新可以被中断。

这主要通过两个核心 API 实现：用于组件的 `useTransition` Hook 和独立的 `startTransition` 函数。

## `useTransition` Hook

`useTransition` Hook 是在函数组件中使用过渡的标准方法。它为过渡的挂起状态提供了一个有状态的值，并提供一个启动过渡的函数。

有关性能相关 Hook 的详细指南，请参阅 [Performance Hooks](./hooks-performance.md) 文档。

### 用法

调用 `useTransition` 会返回一个包含两个元素的数组：

1.  `isPending` (布尔值)：一个标志，当过渡处于活动状态时为 `true`。你可以用它来显示加载指示器或其他挂起状态的 UI。
2.  `startTransition` (函数)：一个函数，你可以用它来包装状态更新，从而将其标记为过渡。

```javascript
const [isPending, startTransition] = useTransition();
```

### 示例：筛选列表

假设你有一个用于筛选大型列表的输入框。如果没有过渡，快速输入可能会导致 UI 卡顿，因为它会尝试在每次按键时重新渲染列表。通过使用 `useTransition`，你可以保持输入框的响应性，同时让列表在后台更新。

```jsx
import { useState, useTransition } from 'react';

function App() {
  const [isPending, startTransition] = useTransition();
  const [inputValue, setInputValue] = useState('');
  const [filterTerm, setFilterTerm] = useState('');

  const handleInputChange = (e) => {
    // 立即更新输入框 - 这是一个紧急更新。
    setInputValue(e.target.value);

    // 将列表筛选逻辑包装在过渡中 - 这是一个非紧急更新。
    startTransition(() => {
      setFilterTerm(e.target.value);
    });
  };

  return (
    <div>
      <input type="text" value={inputValue} onChange={handleInputChange} />
      {isPending && <p>正在更新列表...</p>}
      {/* 一个根据 `filterTerm` 渲染列表的组件 */}
      {/* <FilteredList term={filterTerm} /> */}
    </div>
  );
}
```

在此示例中，`inputValue` 状态会立即更新，因此用户可以无延迟地看到自己输入的内容。而可能触发高开销重新渲染的 `filterTerm` 状态，则在 `startTransition` 中更新。React 会以较低的优先级处理此更新，并且在过渡完成前，`isPending` 标志将为 true。

## `startTransition` 函数

React 还导出了一个独立的 `startTransition` 函数，当 `useTransition` Hook 不可用时（例如在数据处理库或 React 组件之外），可以使用该函数。

### 用法

你可以直接从 React 导入 `startTransition`，并向其传递一个包含非紧急状态更新的回调函数。

```javascript
import { startTransition } from 'react';

// 某些事件处理程序或数据获取逻辑
function handleUpdate() {
  // 此回调内的状态更新被标记为非紧急。
  startTransition(() => {
    // 例如，setSomeState(newValue);
  });
}
```

### 参数

| Name      | Type         | Description                                                                                                                                                             |
| :-------- | :----------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `scope`   | `() => void` | 一个包含一个或多个状态更新（例如 `setState`）的函数。React 会立即调用此函数，并将其中的任何更新都视为过渡。          |
| `options` | `object`     | *（可选）* 一个包含选项的对象。如果 `enableTransitionTracing` 功能标志已开启，你可以为过渡提供一个 `name`，这有助于使用 React DevTools 进行调试。 |

## 概念模型

过渡允许 React 区分紧急更新和非紧急更新。这可以确保需要即时反馈的用户交互，不会被较慢的后台渲染任务所延迟。

```d2
direction: down

"User-Interaction": {
  shape: person
  label: "用户交互"
}

"High-Priority-Update": {
  shape: rectangle
  label: "紧急更新\n（例如，在输入框中输入）"
  style.stroke: "#ff4d4f"
}

"Low-Priority-Update": {
  shape: rectangle
  label: "过渡更新\n（例如，更新列表）"
  style.stroke: "#faad14"
}

"UI-Render": {
  shape: rectangle
  label: "UI 渲染"
  style.fill: "#f6ffed"
}

"User-Interaction" -> "High-Priority-Update": "触发"
"High-Priority-Update" -> "UI-Render": "高优先级\n不可中断"

"User-Interaction" -> "Low-Priority-Update": "触发"
"Low-Priority-Update" -> "UI-Render": "低优先级\n可被紧急更新中断"
```

## 实验性过渡 API

虽然 `useTransition` 和 `startTransition` 是主要的稳定 API，但 React 团队正在探索与过渡相关的更高级功能。这些 API 尚不稳定，其行为可能会发生变化。

-   **`unstable_addTransitionType`**：此函数允许将特定类型（一个字符串）与当前活动的过渡关联起来。这旨在与视图过渡 (View Transitions) 等功能一起使用，以便根据发生的过渡类型应用不同的动画或行为。
-   **`unstable_startGestureTransition`**：此函数专为与连续用户手势（如拖动或滑动）相关联的过渡而设计。它需要一个 `GestureProvider` 来管理基于手势的过渡的生命周期。

这些 API 可用于实验，但不应在生产环境中使用。有关更多信息，请参阅 [实验性 API](./advanced-experimental.md) 指南。

---

通过利用过渡，你可以构建更复杂、响应更灵敏的用户界面，从而优雅地处理数据密集型更新。要了解其他优化渲染的方法，请继续阅读 [性能 Hooks](./hooks-performance.md) 部分。