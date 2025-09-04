# 性能 Hooks

React 的渲染系统通常很快，但对于复杂的应用程序，你可能会遇到性能瓶颈。性能 Hooks 提供了优化组件的工具，通过记忆化昂贵的计算、防止不必要的重新渲染以及在不阻塞用户交互的情况下调度非紧急的 UI 更新来实现。

这些 Hooks 功能强大，但应审慎使用。过早的优化可能导致代码更加复杂。通常最好的做法是先在没有它们的情况下构建组件，然后使用 React Profiler 等工具来识别真正需要优化的区域。

本节涵盖了专为性能调优设计的主要 Hooks：

<x-cards data-columns="2">
  <x-card data-title="useCallback" data-icon="lucide:save">
    记忆化回调函数，防止它们在每次渲染时被重新创建。
  </x-card>
  <x-card data-title="useMemo" data-icon="lucide:calculator">
    记忆化昂贵计算的结果，仅在依赖项改变时才重新计算。
  </x-card>
  <x-card data-title="useTransition" data-icon="lucide:fast-forward">
    将状态更新标记为非紧急，允许其他更新优先渲染而不会阻塞 UI。
  </x-card>
  <x-card data-title="useDeferredValue" data-icon="lucide:hourglass">
    推迟 UI 非关键部分的更新，保持应用程序的响应性。
  </x-card>
</x-cards>

## useCallback

`useCallback` 返回一个回调函数的记忆化版本。当将回调函数传递给依赖引用相等性以防止不必要渲染的优化子组件时，这非常有用。

### 语法

```javascript
const memoizedCallback = useCallback(callback, deps);
```

### 参数

| Parameter  | Type      | Description                                                                                                                                                              |
| ---------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `callback` | `function`| 要被记忆化的函数。                                                                                                                                             |
| `deps`     | `Array`   | 一个依赖项数组。只有当此数组中的值发生变化时，记忆化的回调函数才会被重新创建。如果省略，则每次渲染都会返回一个新的函数。 |

### 示例

考虑一个 `ProductPage` 组件，它将一个 `handleSubmit` 函数传递给 `ShippingForm` 组件。如果 `ShippingForm` 被 `React.memo` 包裹，对 `handleSubmit` 使用 `useCallback` 可以防止在 `ProductPage` 因其他原因重新渲染时，表单也跟着重新渲染。

```jsx
import React, { useState, useCallback } from 'react';

const ShippingForm = React.memo(function ShippingForm({ onSubmit }) {
  console.log('ShippingForm rendered');
  // ... form implementation
  return <button onClick={onSubmit}>Submit</button>;
});

function ProductPage({ productId }) {
  const [theme, setTheme] = useState('dark');

  const handleSubmit = useCallback(() => {
    // This function's identity is stable across re-renders
    // unless productId changes.
    console.log(`Submitting form for product: ${productId}`);
  }, [productId]);

  return (
    <div>
      <button onClick={() => setTheme(theme === 'dark' ? 'light' : 'dark')}>
        Toggle Theme
      </button>
      <ShippingForm onSubmit={handleSubmit} />
    </div>
  );
}
```
在此示例中，切换主题不会导致 `ShippingForm` 重新渲染，因为 `handleSubmit` 函数的引用保持稳定。

## useMemo

`useMemo` 返回一个记忆化的值。它仅在其依赖项之一发生变化时才重新计算该值，这对于避免在每次渲染时进行昂贵的计算非常有用。

### 语法

```javascript
const memoizedValue = useMemo(create, deps);
```

### 参数

| Parameter | Type       | Description                                                                                                                                                  |
| --------- | ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `create`  | `function` | 一个计算并返回要记忆化的值的函数。                                                                                               |
| `deps`    | `Array`    | 一个依赖项数组。只有当此数组中的值发生变化时，`create` 函数才会被重新执行。如果省略，则每次渲染都会重新计算该值。 |

### 示例

如果你的组件需要过滤一个庞大的项目列表，你可以使用 `useMemo` 来确保过滤操作仅在列表或过滤文本发生变化时运行。

```jsx
import React, { useState, useMemo } from 'react';

function TodoList({ todos, filter }) {
  const visibleTodos = useMemo(() => {
    console.log('Filtering todos...');
    return todos.filter(todo => todo.text.includes(filter));
  }, [todos, filter]); // Only re-runs if 'todos' or 'filter' changes

  return (
    <ul>
      {visibleTodos.map(todo => (
        <li key={todo.id}>{todo.text}</li>
      ))}
    </ul>
  );
}
```
这里，`visibleTodos` 仅在 `todos` 数组或 `filter` 字符串发生变化时才会重新计算，而不会在组件的其他重新渲染时计算。

## useTransition

`useTransition` 是一个允许你在不阻塞 UI 的情况下更新状态的 Hook。它返回一个待处理状态和一个用于包装可能造成中断的状态更新的函数。

### 语法

```javascript
const [isPending, startTransition] = useTransition();
```

### 返回值

一个包含两个值的元组：

| Value             | Type       | Description                                                                                             |
| ----------------- | ---------- | ------------------------------------------------------------------------------------------------------- |
| `isPending`       | `boolean`  | 一个布尔值，如果一个过渡当前处于活动状态，则为 `true`。你可以用它来显示加载状态。 |
| `startTransition` | `function` | 一个接收回调函数的函数。此回调函数内的状态更新被标记为非紧急的过渡。 | 

### 示例

当用户在搜索字段中输入时，你可能希望在过滤搜索结果的同时保持输入的响应性。`useTransition` 可以将搜索结果的状态更新标记为较低优先级的过渡。

```jsx
import React, { useState, useTransition } from 'react';

function SearchResults({ query }) {
  // ... component to render search results
  return <div>Searching for: {query}</div>;
}

function App() {
  const [isPending, startTransition] = useTransition();
  const [inputValue, setInputValue] = useState('');
  const [searchQuery, setSearchQuery] = useState('');

  const handleInputChange = (e) => {
    // Urgent: update the input field immediately
    setInputValue(e.target.value);

    // Non-urgent: wrap the disruptive update in a transition
    startTransition(() => {
      setSearchQuery(e.target.value);
    });
  };

  return (
    <div>
      <input value={inputValue} onChange={handleInputChange} />
      {isPending ? (
        <div>Loading...</div>
      ) : (
        <SearchResults query={searchQuery} />
      )}
    </div>
  );
}
```
在此示例中，即使 `SearchResults` 的渲染速度很慢，文本输入也能保持流畅和响应迅速。在过渡待处理期间，UI 会显示“Loading...”消息。

更多高级用例，请参阅 [过渡](./advanced-transitions.md) 指南。

## useDeferredValue

`useDeferredValue` 接受一个值并返回该值的一个新副本，该副本将推迟到更紧急的更新之后。它与 `useTransition` 类似，但对于你无法直接访问状态设置函数的情况通常更简单。

### 语法

```javascript
const deferredValue = useDeferredValue(value);
```

### 参数

| Parameter | Type  | Description                                                                                                                                     |
| --------- | ----- | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| `value`   | `any` | 要推迟的值。React 最初会使用旧值进行渲染，然后在紧急更新后尝试使用新值进行重新渲染。 |

### 示例

这个 Hook 可以用来实现与 `useTransition` 相同的“边输入边搜索”行为，但方式不同。当值来自 props 或另一个 Hook 时，它特别有用。

```jsx
import React, { useState, useDeferredValue } from 'react';

function SearchResults({ query }) {
  // Memoize the component to show the effect clearly
  const deferredQuery = useDeferredValue(query);
  const isStale = query !== deferredQuery;

  // ... expensive rendering logic based on deferredQuery

  return (
    <div style={{ opacity: isStale ? 0.5 : 1 }}>
      Showing results for "{deferredQuery}"
    </div>
  );
}

function App() {
  const [query, setQuery] = useState('');
  return (
    <div>
      <input value={query} onChange={e => setQuery(e.target.value)} />
      <SearchResults query={query} />
    </div>
  );
}
```
在这里，`SearchResults` 组件会立即接收到 `query`，但使用 `useDeferredValue` 来创建一个 `deferredQuery`。在组件等待新查询被渲染时，`deferredQuery` 会持有之前的值，从而使 UI 保持响应。`isStale` 标志可用于提供视觉反馈，例如将旧结果调暗。

---

掌握了这些性能优化后，你可以探索其他专门的 Hooks。更多信息，请继续阅读 [其他 Hooks](./hooks-other.md) 指南。