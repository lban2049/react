# 性能 Hooks

React 通常开箱即用就很快，但随着应用的增长，某些交互可能会变慢。当处理高开销的计算或频繁重新渲染的大型组件树时，可能会发生这种情况。性能 Hooks 旨在帮助你在这些特定情况下优化组件。

在应用这些优化之前，对你的应用进行性能分析并找出性能瓶颈非常重要。如果使用不当，它们可能会增加不必要的复杂性。本指南涵盖了用于性能调优的主要 Hooks：`useCallback`、`useMemo`、`useTransition` 和 `useDeferredValue`。

对于相关主题，你可能需要回顾 [State Hooks](./hooks-state.md) 和 [Effect Hooks](./hooks-effect.md)，因为性能优化通常涉及有效地管理状态和副作用。

---

## `useCallback`

`useCallback` hook 会记忆化一个回调函数，防止它在每次渲染时被重新创建。当将回调函数传递给依赖引用相等性来防止不必要重新渲染的优化子组件时（例如，用 `React.memo` 包裹的组件），这尤其有用。

### 语法

```javascript
const memoizedCallback = useCallback(
  () => {
    doSomething(a, b);
  },
  [a, b],
);
```

### 参数

| 参数 | 类型 | 描述 |
|---|---|---|
| `callback` | `function` | 要被记忆化的函数。 |
| `deps` | `Array` | 一个依赖项数组。只有当其中一个依赖项发生变化时，回调函数才会被重新创建。 |

### 返回值

`useCallback` 返回一个记忆化版本的的回调函数。只要依赖项没有改变，这个函数的标识就保证是稳定的。

### 示例

考虑一个父组件将一个处理函数传递给一个记忆化的子组件。如果没有 `useCallback`，子组件会在父组件每次渲染时都重新渲染，因为处理函数在内存中是一个新对象。

```javascript
import React, { useState, useCallback } from 'react';

const Button = React.memo(({ onClick, children }) => {
  console.log(`Rendering button: ${children}`);
  return <button onClick={onClick}>{children}</button>;
});

function App() {
  const [count, setCount] = useState(0);
  const [otherState, setOtherState] = useState(false);

  // App 组件每次渲染时，这个函数都会被重新创建，
  // 导致记忆化的 Button 不必要地重新渲染。
  const handleIncrement = () => {
    setCount(c => c + 1);
  };

  // 通过使用 useCallback，只要 `setCount` 不变，
  // 这个函数的引用就是稳定的。
  const handleDecrement = useCallback(() => {
    setCount(c => c - 1);
  }, []); // 空依赖数组意味着该函数永远不会被重新创建

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setOtherState(!otherState)}>Toggle Other State</button>
      <hr />
      <Button onClick={handleIncrement}>Increment (No useCallback)</Button>
      <Button onClick={handleDecrement}>Decrement (With useCallback)</Button>
    </div>
  );
}
```
在这个例子中，点击“Toggle Other State”按钮会导致 `App` 组件重新渲染。你会在控制台中看到“Rendering button: Increment (No useCallback)”，但不会看到 decrement 按钮的这条信息，因为得益于 `useCallback`，它的 `onClick` prop 是稳定的。

---

## `useMemo`

`useMemo` hook 会记忆化一个计算值。它运行一个函数并缓存其结果。在后续的渲染中，只要依赖项没有改变，它将返回缓存的结果而不会重新执行该函数。这对于避免在每次渲染时都进行高开销的计算很有用。

### 语法

```javascript
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

### 参数

| 参数 | 类型 | 描述 |
|---|---|---|
| `create` | `function` | 计算要被记忆化的值的函数。 |
| `deps` | `Array` | 一个依赖项数组。只有当其中一个依赖项发生变化时，该值才会被重新计算。 |

### 返回值

`useMemo` 返回记忆化的值。该值只会在依赖项改变时更新。

### 示例

想象一个组件需要过滤一个庞大的项目列表。这种过滤操作可能会很慢。`useMemo` 可以防止这个高开销的操作在组件因其他原因重新渲染时都运行一次。

```javascript
import React, { useState, useMemo } from 'react';

const largeList = Array.from({ length: 10000 }, (_, i) => ({ id: i, name: `Item ${i}` }));

function FilterableList() {
  const [filter, setFilter] = useState('');
  const [otherState, setOtherState] = useState(false);

  const filteredList = useMemo(() => {
    console.log('Filtering list...');
    if (!filter) return largeList;
    return largeList.filter(item => item.name.includes(filter));
  }, [filter]); // 仅在 `filter` 改变时重新运行

  return (
    <div>
      <input 
        type="text" 
        value={filter} 
        onChange={e => setFilter(e.target.value)} 
        placeholder="Filter items..."
      />
      <button onClick={() => setOtherState(!otherState)}>Toggle Other State</button>
      <p>Items found: {filteredList.length}</p>
    </div>
  );
}
```
在这里，高开销的过滤逻辑被包裹在 `useMemo` 中。当你点击“Toggle Other State”按钮时，组件会重新渲染，但“Filtering list...”不会被打印到控制台，因为 `filter` 依赖项没有改变。计算被跳过，而是使用了缓存的 `filteredList`。

---

## `useTransition`

`useTransition` hook 允许你更新状态而无需阻塞 UI。它专为状态更新可能导致明显延迟的情况设计，例如响应用户输入过滤大型数据集。它将某些状态更新标记为“过渡”，这告诉 React 如果有更紧急的更新（如打字）进来，它们可以被中断。

### 语法

```javascript
const [isPending, startTransition] = useTransition();
```

### 返回值

`useTransition` 返回一个包含两个元素的数组：

| 值 | 类型 | 描述 |
|---|---|---|
| `isPending` | `boolean` | 一个布尔值，在过渡挂起期间为 `true`。你可以用它来显示加载指示器。 |
| `startTransition` | `function` | 一个用于包裹状态更新的函数。React 会将此包裹函数内的任何状态更新视为非紧急。 |

### 示例

这个例子展示了 `useTransition` 如何在列表被过滤时保持文本输入的响应性。

```javascript
import React, { useState, useTransition } from 'react';

// 假设 List 是一个渲染大量项目的组件
function List({ items }) { ... }

function App() {
  const [isPending, startTransition] = useTransition();
  const [query, setQuery] = useState('');
  const [filteredItems, setFilteredItems] = useState([]);

  const handleChange = (e) => {
    // 紧急：立即更新输入字段
    setQuery(e.target.value);

    // 非紧急：将慢速的状态更新包裹在过渡中
    startTransition(() => {
      // 这可能是一个慢速操作
      const items = getFilteredItems(e.target.value);
      setFilteredItems(items);
    });
  };

  return (
    <div>
      <input onChange={handleChange} value={query} type="text" />
      {isPending && <div>Loading...</div>}
      <List items={filteredItems} />
    </div>
  );
}
```
当用户在输入框中输入时，`setQuery` 会立即更新，因此输入字段感觉响应迅速。`startTransition` 内部的 `setFilteredItems` 更新在后台运行。在它运行时，`isPending` 为 `true`，允许你显示加载状态。

---

## `useDeferredValue`

`useDeferredValue` 与 `useTransition` 类似，但用途略有不同。它让你能够推迟 UI 非紧急部分的重新渲染。它接受一个值并返回该值的新副本，这个新副本在紧急渲染期间会“滞后于”原始值。当值来自父组件且你无法控制状态更新本身时，这非常有用。

### 语法

```javascript
const deferredValue = useDeferredValue(value);
```

### 参数

| 参数 | 类型 | 描述 |
|---|---|---|
| `value` | `any` | 你想要推迟的值。 |

### 返回值

返回一个值的延迟版本。在初始渲染期间，返回的值将与提供的值相同。在更新期间，React 会首先使用旧值重新渲染，然后在后台尝试使用新值进行重新渲染。

### 示例

让我们使用 `useDeferredValue` 重构之前的过滤示例。

```javascript
import React, { useState, useDeferredValue, useMemo } from 'react';

// 假设 SearchResults 是一个渲染大型列表的组件
function SearchResults({ query }) {
  const items = useMemo(() => getFilteredItems(query), [query]);
  return <List items={items} />;
}

function App() {
  const [query, setQuery] = useState('');
  const deferredQuery = useDeferredValue(query);

  const isStale = query !== deferredQuery;

  return (
    <div>
      <input onChange={e => setQuery(e.target.value)} value={query} type="text" />
      <div style={{ opacity: isStale ? 0.5 : 1 }}>
        <SearchResults query={deferredQuery} />
      </div>
    </div>
  );
}
```
在这种设置中，`App` 组件会立即更新 `query` 状态。然而，`deferredQuery` 会滞后。React 将以较低的优先级使用 `deferredQuery` 更新 `SearchResults` 组件。这可以保持输入的响应性，并且我们可以使用 `isStale` 标志来提供视觉反馈（如降低不透明度），表明结果正在更新。

### 该使用哪一个？

- 当你可以访问状态设置函数时，使用 `useTransition`。
- 当你无法直接控制状态更新，而只有导致慢速重新渲染的值时，使用 `useDeferredValue`。

---

### 总结

性能 Hooks 是保持你的 React 应用快速和响应迅速的强大工具。通过记忆化函数和值以及推迟非紧急的 UI 更新，你可以解决许多常见的性能问题。

```d2
direction: down

start: "我的组件是否很慢？"

check_cause: "原因是什么？" {
  shape: diamond
}

re_render: "子组件不必要的重新渲染？"
expensive_calc: "每次渲染时都有高开销的计算？"
ui_block: "状态更新是否阻塞了 UI 交互？"

use_callback: "使用 useCallback 记忆化作为 props 传递的函数。"
use_memo: "使用 useMemo 记忆化计算结果。"
use_transition: "使用 useTransition 或 useDeferredValue 推迟更新。"

start -> check_cause
check_cause -> re_render
check_cause -> expensive_calc
check_cause -> ui_block

re_render -> use_callback
expensive_calc -> use_memo
ui_block -> use_transition
```

优化组件后，你可以继续探索 [其他 Hooks](./hooks-other.md) 或深入了解 [过渡](./advanced-transitions.md) 指南中的更高级模式。