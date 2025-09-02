# Transition

React 中的 Transition 是一个核心功能，用于管理复杂的 UI 更新，而不会阻塞主线程。它们允许你将某些状态更新标记为非紧急，从而使 React 即使在执行繁重的渲染任务时也能保持用户界面的响应能力。这对于可能较慢的更新特别有用，例如过滤大型列表或重新渲染复杂的可视化效果。

本指南探讨了 Transition 的概念以及实现它们的主要 API：`startTransition` 函数和 `useTransition` Hook。

如果想查阅专门介绍该 Hook 的指南，你也可以参考 [性能 Hooks](./hooks-performance.md) 文档。

## 核心问题：紧急更新与非紧急更新

默认情况下，React 将所有状态更新都视为紧急更新。当用户与应用程序交互时（例如，在输入框中输入），React 会尝试立即重新渲染组件树。如果这次重新渲染的计算量很大，它可能会阻塞主线程，导致 UI 冻结或卡顿。这会带来糟糕的用户体验。

Transition 通过允许你将更新分为两类来解决此问题：

1. **紧急更新**：需要立即反馈的直接用户交互，如输入、点击或按键。
2. **Transition 更新**：不需要立即显示的 UI 变化，例如显示搜索结果或过滤数据。

当你将更新包装在 Transition 中时，你是在告诉 React 可以延迟此渲染，更重要的是，如果出现更紧急的更新，可以中断它。

下图说明了标准阻塞更新与使用 Transition 处理的更新之间的区别。

```d2
shape: sequence_diagram

User: 用户
UI: UI 线程
React: React 渲染器

subgraph "标准阻塞更新" {
  direction: right
  User -> UI: "在搜索框中输入"
  UI -> React: "为输入框和列表设置状态"
  React: "渲染整个组件（可能很慢）" {style: {fill: "#ffcaca"}}
  UI <- React: "渲染完成"
  note right of UI: "由于列表过滤延迟了输入，UI 感觉卡顿。"
}

subgraph "使用 Transition 更新" {
  direction: right
  User -> UI: "在搜索框中输入"
  UI -> React: "紧急 setState（输入框值）"
  UI <- React: "输入框立即更新"
  UI -> React: "startTransition(为列表设置状态)"
  React: "开始对列表进行非紧急渲染" {style: {fill: "#caffca"}}
  User -> UI: "再次输入"
  UI -> React: "新的紧急 setState"
  React: "中断列表渲染，首先处理新的输入更新" {style: {stroke-dash: 2}}
  UI <- React: "输入框再次立即更新"
}
```

## `startTransition`

`startTransition` 函数允许你将回调函数内的状态更新标记为 Transition。它可以在任何地方使用，包括组件外部（例如，在数据库中）。

### 语法

```javascript
import { startTransition } from 'react';

startTransition(scope, options?);
```

### 参数

| Name | Type | Description |
| :-------- | :--------- | :------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `scope` | `() => void` | 一个包含你希望标记为 transition 的状态更新的函数。React 会立即调用此函数，且不带任何参数。 |
| `options` | `object` | *（可选）* 一个包含 transition 配置的对象。目前，它仅支持用于 React DevTools 性能分析的 `name`。 |

### 示例

以下是如何使用 `startTransition` 来防止缓慢的数据过滤阻塞用户输入。

```javascript
import { startTransition } from 'react';

// 假设 setInputValue 和 setSearchQuery 来自 useState
function handleSearch(e) {
  // 紧急：显示用户正在输入的内容
  setInputValue(e.target.value);

  // 非紧急：显示过滤后的结果
  startTransition(() => {
    setSearchQuery(e.target.value);
  });
}
```

在此示例中，当用户输入时，输入字段会立即更新。而 `setSearchQuery` 更新可能会触发列表的昂贵重新渲染，它被包装在 `startTransition` 中。这会告诉 React 该更新可以被推迟，从而确保输入框保持响应。

## `useTransition` Hook

对于在函数组件内部使用，推荐使用 `useTransition` Hook。它提供与 `startTransition` 相同的功能，但还包含一个挂起状态指示器，这对于向用户提供反馈非常有用。

### 语法

该 Hook 返回一个包含两个值的数组：

```javascript
import { useTransition } from 'react';

const [isPending, startTransition] = useTransition();
```

### 返回值

| Name | Type | Description |
| :--------------- | :------------ | :------------------------------------------------------------------------------------------------------ |
| `isPending` | `boolean` | 一个布尔值，如果 transition 当前处于活动和挂起状态，则为 `true`，否则为 `false`。 |
| `startTransition`| `() => void` | 用于包装你的非紧急状态更新的函数。 |

### 带挂起状态的示例

在前面示例的基础上，我们可以使用 `isPending` 来在列表过滤期间显示一个加载指示器。

```javascript
import { useState, useTransition } from 'react';

function SearchPage({ initialItems }) {
  const [isPending, startTransition] = useTransition();
  const [filter, setFilter] = useState('');

  const filteredItems = initialItems.filter(item => item.includes(filter));

  function updateFilter(e) {
    startTransition(() => {
      setFilter(e.target.value);
    });
  }

  return (
    <>
      <input type="text" onChange={updateFilter} />
      <div style={{ opacity: isPending ? 0.5 : 1 }}>
        {/* filteredItems 列表 */}
        <ul>
          {filteredItems.map(item => <li key={item}>{item}</li>)}
        </ul>
      </div>
    </>
  );
}
```

在此组件中，当 React 在后台忙于渲染新的过滤项时，列表会变为半透明，从而提供更新正在进行中的视觉反馈。

## 高级和实验性功能

Transition 机制是可扩展的，支持更高级、通常是实验性的用例。

<x-cards data-columns="2">
  <x-card data-title="视图转换" data-icon="lucide:gallery-vertical">
    `unstable_addTransitionType` 函数允许将一个 transition 与特定类型关联，其他功能（如 `unstable_ViewTransition`）会使用该类型在不同 DOM 状态之间创建动画过渡效果。
  </x-card>
  <x-card data-title="手势转换" data-icon="lucide:move">
    `unstable_startGestureTransition` 函数专为处理由用户手势（例如滑动）启动的 transition 而设计。它有助于创建与手势本身紧密耦合的更平滑的 UI 更新。
  </x-card>
</x-cards>

这些 API 是不稳定的，其用法可能会发生变化。在此提及它们是为了完整地展示 React Transition 系统的功能。

## 总结

Transition 是构建高性能和响应迅速的 React 应用程序的关键功能。通过区分紧急和非紧急更新，你可以确保即使在执行繁重的渲染工作时，你的应用程序 UI 也不会感觉卡顿。

- 在组件中使用 **`useTransition`** 以获取用于用户反馈的挂起状态。
- 在组件外部或不需要挂起状态时使用 **`startTransition`**。

### 后续步骤

现在你已经了解了 Transition，你可能想探索相关的性能功能或深入研究其他高级主题。

<x-cards>
  <x-card data-title="性能 Hooks" data-icon="lucide:gauge-circle" data-href="/hooks/performance">
    了解其他与性能相关的 Hooks，例如与 `useTransition` 密切相关的 `useDeferredValue`。
  </x-card>
  <x-card data-title="实验性 API" data-icon="lucide:flask-conical" data-href="/advanced/experimental">
    探索 React 中的其他实验性和不稳定 API，以一窥其未来功能。
  </x-card>
</x-cards>