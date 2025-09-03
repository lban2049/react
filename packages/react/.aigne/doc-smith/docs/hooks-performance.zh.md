# 性能 Hook

优化渲染性能是构建快速、响应迅速的 React 应用程序的关键一环。本节介绍了旨在帮助你通过记忆化值和函数，以及优雅地管理非紧急 UI 更新，来控制和改善组件渲染方式的 Hook。

这些 Hook 是强大的工具，但只应在已确定存在性能问题时使用。过早的优化可能导致代码更加复杂，却带不来实际的好处。

以下是 React 中可用的主要性能 Hook：

<x-cards data-columns="2">
  <x-card data-title="useCallback" data-icon="lucide:function-square">
    记忆化回调函数，防止它们在每次渲染时被重新创建。
  </x-card>
  <x-card data-title="useMemo" data-icon="lucide:binary">
    记忆化高开销计算的结果，仅在依赖项改变时才重新计算。
  </x-card>
  <x-card data-title="useTransition" data-icon="lucide:fast-forward">
    将状态更新标记为非紧急的过渡，从而在繁重的渲染期间保持 UI 的响应性。
  </x-card>
  <x-card data-title="useDeferredValue" data-icon="lucide:hourglass">
    推迟更新一个值，允许 UI 在准备新值的同时显示旧值。
  </x-card>
</x-cards>

---

## `useCallback`

`useCallback` Hook 返回一个记忆化版本的回调函数，该函数仅在某个依赖项改变时才会更新。这在将回调函数传递给依赖引用相等性以防止不必要渲染的优化子组件时非常有用。

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

| Parameter  | Type       | Description                                                                                             | Required |
| :--------- | :--------- | :------------------------------------------------------------------------------------------------------ | :------- |
| `callback` | `function` | 要记忆化的函数。                                                                                        | 是       |
| `deps`     | `Array`    | 一个依赖项数组。如果其中任何一个依赖项的值发生变化，回调函数将被重新创建。                         | 是       |

### 示例

假设有一个 `SearchableList` 组件接收一个 `onSearch` 函数。如果父组件重新渲染，会创建一个新的 `onSearch` 函数，这会导致 `SearchableList` 即使被记忆化了也会重新渲染。`useCallback` 解决了这个问题。

```javascript
import React, { useState, useCallback } from 'react';
import { memo } from 'react';

// 假设此子组件已使用 React.memo 进行优化
const SearchableList = memo(({ onSearch }) => {
  console.log('SearchableList rendered');
  // ... 列表渲染逻辑
  return <button onClick={() => onSearch('query')}>Search</button>;
});

function ParentComponent() {
  const [count, setCount] = useState(0);

  // 如果没有 useCallback，该函数会在 ParentComponent 的每次渲染时重新创建，
  // 导致 SearchableList 不必要地重新渲染。
  const handleSearch = useCallback((query) => {
    console.log('Searching for:', query);
  }, []); // 空依赖数组意味着该函数只在初次渲染时创建一次

  return (
    <div>
      <button onClick={() => setCount(count + 1)}>Increment Count: {count}</button>
      <SearchableList onSearch={handleSearch} />
    </div>
  );
}
```
在此示例中，点击“Increment Count”按钮会重新渲染 `ParentComponent`，但由于 `handleSearch` 被包裹在 `useCallback` 中且依赖数组为空，因此传递给 `SearchableList` 的是同一个函数实例。`React.memo` 发现 `onSearch` prop 没有改变，因此跳过了对列表的重新渲染。

---

## `useMemo`

`useMemo` Hook 返回一个记忆化的值。它仅在某个依赖项改变时才重新执行函数以计算新值。这对于避免在每次渲染时都进行高开销的计算非常有用。

### 语法

```javascript
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

### 参数

| Parameter | Type       | Description                                                                                              |
| :-------- | :--------- | :------------------------------------------------------------------------------------------------------- |
| `create`  | `function` | 返回待记忆化值的函数。该函数应为纯函数。                                                                 |
| `deps`    | `Array`    | 一个依赖项数组。如果其中任何一个依赖项发生变化，`create` 函数将被重新执行。                         |

### 示例

如果你有一个需要筛选大型列表的组件，此操作可能会很慢。使用 `useMemo` 可以确保仅在列表或筛选条件改变时才执行筛选。

```javascript
import React, { useState, useMemo } from 'react';

function ProductList({ products, filterTerm }) {
  // 只有在 'products' 或 'filterTerm' 改变时，
  // 这个高开销的筛选操作才会重新运行。
  const visibleProducts = useMemo(() => {
    console.log('Filtering products...');
    return products.filter(p => p.name.includes(filterTerm));
  }, [products, filterTerm]);

  return (
    <ul>
      {visibleProducts.map(product => (
        <li key={product.id}>{product.name}</li>
      ))}
    </ul>
  );
}
```
如果没有 `useMemo`，`products.filter` 会在 `ProductList` 因任何原因重新渲染时都运行一次，这可能会降低 UI 的速度。有了 `useMemo`，如果输入相同，则会跳过这项高开销的工作。

---

## `useTransition`

`useTransition` Hook 允许你在不阻塞 UI 的情况下更新状态。它返回一个表示过渡待定状态的有状态值和一个用于启动过渡的函数。

这对于状态更新可能导致明显延迟的情况非常理想，例如在响应用户输入时筛选大型数据集。

### 语法

```javascript
const [isPending, startTransition] = useTransition();
```

### 返回值

`useTransition` 返回一个包含两个元素的数组：

| Item              | Type       | Description                                                                                                   |
| :---------------- | :--------- | :------------------------------------------------------------------------------------------------------------ |
| `isPending`       | `boolean`  | 一个标志，当过渡处于待定状态时为 `true`。你可以用它来显示加载指示器。                                    |
| `startTransition` | `function` | 一个接收回调函数的函数。此回调函数内的任何状态更新都被标记为非紧急的过渡。                               |

### 示例

在此示例中，在输入字段中键入内容会更新两个状态：输入值（紧急）和筛选后的列表（非紧急）。通过将列表更新包裹在 `startTransition` 中，我们确保了即使筛选列表很慢，输入字段也能保持响应。

```javascript
import React, { useState, useTransition } from 'react';

const largeList = Array.from({ length: 10000 }, (_, i) => `Item ${i + 1}`);

function App() {
  const [isPending, startTransition] = useTransition();
  const [inputValue, setInputValue] = useState('');
  const [filteredList, setFilteredList] = useState(largeList);

  const handleChange = (e) => {
    // 紧急更新：立即显示用户正在输入的内容
    setInputValue(e.target.value);

    // 非紧急更新：包裹在 startTransition 中
    startTransition(() => {
      setFilteredList(largeList.filter(item => item.includes(e.target.value)));
    });
  };

  return (
    <div>
      <input type="text" value={inputValue} onChange={handleChange} />
      {isPending && <p>Loading list...</p>}
      <ul>
        {filteredList.map((item, index) => <li key={index}>{item}</li>)}
      </ul>
    </div>
  );
}
```

---

## `useDeferredValue`

`useDeferredValue` Hook 接受一个值并返回该值的新副本，该副本将推迟到更紧急的更新之后。如果当前渲染是由紧急更新（如用户输入）引起的，React 将返回先前的值，然后在紧急渲染完成后再渲染新值。

这个 Hook 与 `useTransition` 类似，但当无法直接控制状态设置调用时，通常更容易使用。

### 语法

```javascript
const deferredValue = useDeferredValue(value, initialValue);
```

### 参数

| Parameter      | Type  | Description                                                                                             |
| :------------- | :---- | :------------------------------------------------------------------------------------------------------ |
| `value`        | `any` | 你想要推迟的值。                                                                                        |
| `initialValue` | `any` | （可选）在第一个延迟值可用之前使用的初始值。                                                                  |

### 示例

此示例创建了一个 `ProductList`，它接收一个搜索查询作为 prop。通过对查询使用 `useDeferredValue`，列表可以使用延迟的查询进行重新渲染，从而防止主输入字段出现延迟。

```javascript
import React, { useState, useDeferredValue } from 'react';

const largeList = Array.from({ length: 10000 }, (_, i) => `Product ${i + 1}`);

function ProductList({ query }) {
  const deferredQuery = useDeferredValue(query);
  const list = useMemo(() => 
    largeList.filter(item => item.includes(deferredQuery)),
    [deferredQuery]
  );

  return (
    <ul>
      {list.map((item, index) => <li key={index}>{item}</li>)}
    </ul>
  );
}

function App() {
  const [query, setQuery] = useState('');

  return (
    <div>
      <input type="text" value={query} onChange={e => setQuery(e.target.value)} placeholder="Search products..."/>
      <ProductList query={query} />
    </div>
  );
}

```
在这里，当用户输入时，`query` 状态会立即更新，保持输入的响应性。`ProductList` 组件接收到这个新的 `query`，但 `useDeferredValue` 告诉 React，它可以在后台渲染新列表的同时，继续显示旧的列表（基于先前的 `deferredQuery`）。

---

通过这些 Hook，你可以有效地管理复杂的渲染逻辑，并确保流畅的用户体验。有关过渡的更高级模式，请参阅[过渡](./advanced-transitions.md)指南。
