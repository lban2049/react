# 性能 Hook

优化性能是构建响应迅速、流畅的用户界面的一个关键方面。React 提供了一套专门的 Hook，旨在帮助你避免不必要的计算和渲染，确保你的应用在规模扩大时仍能保持快速。这些 Hook 是用于记忆化和管理非紧急 UI 更新的强大工具。

本指南涵盖了主要的性能 Hook。有关状态管理的 Hook，请参阅 [State Hooks](./hooks-state.md)，有关副作用的 Hook，请参阅 [Effect Hooks](./hooks-effect.md)。

## useCallback

`useCallback` Hook 会记忆化一个回调函数，防止它在每次渲染时被重新创建。当将回调函数传递给依赖引用相等性来防止不必要重渲染的优化子组件时，这尤其有用。

`useCallback` 将返回一个记忆化版本的函数，该函数仅在某个依赖项发生变化时才会改变。

### 语法

```javascript React Hook: useCallback icon=logos:react
const memoizedCallback = useCallback(
  () => {
    doSomething(a, b);
  },
  [a, b],
);
```

### 参数

| Parameter | Type | Description |
|---|---|---|
| `callback` | `function` | 要记忆化的函数。 |
| `deps` | `Array<any>` | 一个依赖项列表。只有当此数组中的某个值发生变化时，才会重新创建回调函数。 |

### 示例

考虑一个用 `React.memo` 包装的子组件。如果父组件在每次渲染时都传递一个新的函数实例，子组件将会不必要地重新渲染。`useCallback` 解决了这个问题。

```javascript ParentComponent.js icon=logos:react
import React, { useState, useCallback } from 'react';
import MemoizedChild from './MemoizedChild';

function ParentComponent() {
  const [count, setCount] = useState(0);
  const [otherState, setOtherState] = useState(false);

  // 只有当 `count` 改变时，这个函数才会被重新创建。
  const handleClick = useCallback(() => {
    console.log(`Button clicked! Count is ${count}`);
  }, [count]);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment Count</button>
      <button onClick={() => setOtherState(!otherState)}>Toggle Other State</button>
      {/* 除非 count 改变，否则 handleClick 函数的标识在多次重渲染之间是稳定的 */}
      <MemoizedChild onClick={handleClick} />
    </div>
  );
}
```

在此示例中，点击“Toggle Other State”会重新渲染 `ParentComponent`，但不会重新创建 `handleClick`。因此，`MemoizedChild` 不会重新渲染，因为它的 `onClick` prop 没有改变。

## useMemo

与 `useCallback` 类似，`useMemo` 用于记忆化，但它记忆化的是函数调用的*结果*，而不是函数本身。它非常适合用来避免在每次渲染时都进行高开销的计算。

`useMemo` 仅在某个依赖项发生变化时才会重新计算记忆化的值。

### 语法

```javascript React Hook: useMemo icon=logos:react
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

### 参数

| Parameter | Type | Description |
|---|---|---|
| `create` | `() => T` | 返回待记忆化值的函数。 |
| `deps` | `Array<any>` | 一个依赖项列表。只有当某个依赖项发生变化时，才会重新计算该值。 |

### 示例

如果你有一个组件需要从一个大数据集中计算一个值，`useMemo` 可以防止这个计算在组件因其他原因重新渲染时每次都运行。

```javascript DataDisplay.js icon=logos:react
import React, { useState, useMemo } from 'react';

function expensiveCalculation(num) {
  console.log('Performing expensive calculation...');
  // 想象这里有一个繁重的计算
  let total = 0;
  for (let i = 0; i < 1000000000; i++) {
    total += num;
  }
  return total % 100;
}

function DataDisplay({ data }) {
  const [theme, setTheme] = useState('light');

  // 只有当 `data.number` 改变时，expensiveCalculation 才会运行。
  const computedValue = useMemo(() => expensiveCalculation(data.number), [data.number]);

  return (
    <div className={theme}>
      <p>Computed Value: {computedValue}</p>
      <button onClick={() => setTheme(theme === 'light' ? 'dark' : 'light')}>
        Toggle Theme
      </button>
    </div>
  );
}
```

在这里，切换主题不会触发 `expensiveCalculation`，因为它的依赖项 `data.number` 没有改变。

## useTransition

`useTransition` 让你可以在不阻塞 UI 的情况下更新状态。它将特定的状态更新标记为“过渡”，这会告诉 React，如果更紧急的更新（如在输入框中键入）到达，这些更新可以被中断。

这个 Hook 非常适合在数据获取或渲染大型组件树时保持应用的响应性。

### 语法

它返回一个包含两个元素的数组：

```javascript React Hook: useTransition icon=logos:react
const [isPending, startTransition] = useTransition();
```

### 返回值

| Item | Type | Description |
|---|---|---|
| `isPending` | `boolean` | 一个布尔标志，在过渡挂起期间为 `true`。你可以用它来显示加载指示器。 |
| `startTransition` | `(callback: () => void) => void` | 一个函数，允许你将状态更新标记为过渡。 |

### 示例

在筛选一个大型列表时，将筛选逻辑包装在 `startTransition` 中可以保持输入字段的响应性。

```javascript FilterableList.js icon=logos:react
import React, { useState, useTransition } from 'react';

const allItems = Array.from({ length: 10000 }, (_, i) => `Item ${i + 1}`);

function FilterableList() {
  const [isPending, startTransition] = useTransition();
  const [filter, setFilter] = useState('');
  const [filteredItems, setFilteredItems] = useState(allItems);

  const handleFilterChange = (e) => {
    const nextFilter = e.target.value;
    setFilter(nextFilter);
    // 输入状态会立即更新，但列表的更新会被推迟。
    startTransition(() => {
      setFilteredItems(allItems.filter(item => item.includes(nextFilter)));
    });
  };

  return (
    <div>
      <input type="text" value={filter} onChange={handleFilterChange} />
      {isPending && <p>Updating list...</p>}
      <ul>
        {filteredItems.map(item => <li key={item}>{item}</li>)}
      </ul>
    </div>
  );
}
```

## useDeferredValue

`useDeferredValue` 让你能够推迟 UI 中非紧急部分的重新渲染。它的目的与 `useTransition` 相似，但当你无法直接控制设置状态的调用时，它通常更易于使用。

该 Hook 返回一个值的延迟版本，该版本在紧急重新渲染期间会“落后于”最新值。

### 语法

```javascript React Hook: useDeferredValue icon=logos:react
const deferredValue = useDeferredValue(value);
```

### 参数

| Parameter | Type | Description |
|---|---|---|
| `value` | `T` | 你想要延迟的值。 |
| `initialValue` | `T` | （可选）在计算出第一个延迟值之前使用的初始值。 |

### 示例

这个示例实现了与 `useTransition` 示例相同的结果，但采用了不同的方法。我们在使用 `filter` 值计算列表之前，先延迟了它本身。

```javascript DeferredList.js icon=logos:react
import React, { useState, useDeferredValue, useMemo } from 'react';

const allItems = Array.from({ length: 10000 }, (_, i) => `Item ${i + 1}`);

function List({ items }) {
  return <ul>{items.map(item => <li key={item}>{item}</li>)}</ul>;
}

function DeferredList() {
  const [filter, setFilter] = useState('');
  const deferredFilter = useDeferredValue(filter);

  const filteredItems = useMemo(() => {
    return allItems.filter(item => item.includes(deferredFilter));
  }, [deferredFilter]);

  return (
    <div>
      <input type="text" value={filter} onChange={e => setFilter(e.target.value)} />
      <List items={filteredItems} />
    </div>
  );
}
```

当用户输入时，`input` 会随 `filter` 状态立即更新。然而，`deferredFilter` 值会延迟更新，因此高开销的列表筛选和重新渲染不会阻塞用户的输入。

---

通过利用这些性能 Hook，你可以构建高度优化和响应迅速的 React 应用。要了解 React 工具包中的更多工具，请继续阅读 [Other Hooks](./hooks-other.md)。