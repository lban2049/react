# Performance Hooks

Optimizing rendering performance is a key aspect of building fast and responsive React applications. This section covers hooks designed to help you control and improve how your components render by memoizing values and functions, and by managing non-urgent UI updates gracefully.

These hooks are powerful tools, but they should be used when a performance problem has been identified. Premature optimization can lead to more complex code without tangible benefits.

Here are the primary performance hooks available in React:

<x-cards data-columns="2">
  <x-card data-title="useCallback" data-icon="lucide:function-square">
    Memoizes callback functions, preventing them from being recreated on every render.
  </x-card>
  <x-card data-title="useMemo" data-icon="lucide:binary">
    Memoizes the result of an expensive calculation, recomputing it only when dependencies change.
  </x-card>
  <x-card data-title="useTransition" data-icon="lucide:fast-forward">
    Marks state updates as non-urgent transitions, keeping the UI responsive during heavy rendering.
  </x-card>
  <x-card data-title="useDeferredValue" data-icon="lucide:hourglass">
    Defers updating a value, allowing the UI to show an older value while a new one is being prepared.
  </x-card>
</x-cards>

---

## `useCallback`

The `useCallback` hook returns a memoized version of a callback function that only changes if one of its dependencies has changed. This is useful when passing callbacks to optimized child components that rely on reference equality to prevent unnecessary renders.

### Syntax

```javascript
const memoizedCallback = useCallback(
  () => {
    doSomething(a, b);
  },
  [a, b],
);
```

### Parameters

| Parameter  | Type       | Description                                                                                             | Required |
| :--------- | :--------- | :------------------------------------------------------------------------------------------------------ | :------- |
| `callback` | `function` | The function to be memoized.                                                                            | Yes      |
| `deps`     | `Array`    | An array of dependencies. The callback will be recreated if any of these dependencies change in value. | Yes      |

### Example

Consider a `SearchableList` component that receives an `onSearch` function. If the parent component re-renders, a new `onSearch` function is created, causing `SearchableList` to re-render even if it's memoized. `useCallback` solves this.

```javascript
import React, { useState, useCallback } from 'react';
import { memo } from 'react';

// Assume this child component is optimized with React.memo
const SearchableList = memo(({ onSearch }) => {
  console.log('SearchableList rendered');
  // ... list rendering logic
  return <button onClick={() => onSearch('query')}>Search</button>;
});

function ParentComponent() {
  const [count, setCount] = useState(0);

  // This function would be recreated on every render of ParentComponent
  // without useCallback, causing SearchableList to re-render unnecessarily.
  const handleSearch = useCallback((query) => {
    console.log('Searching for:', query);
  }, []); // Empty dependency array means the function is created only once

  return (
    <div>
      <button onClick={() => setCount(count + 1)}>Increment Count: {count}</button>
      <SearchableList onSearch={handleSearch} />
    </div>
  );
}
```
In this example, clicking the 'Increment Count' button re-renders `ParentComponent`, but because `handleSearch` is wrapped in `useCallback` with an empty dependency array, the same function instance is passed to `SearchableList`. `React.memo` sees that the `onSearch` prop hasn't changed, and skips re-rendering the list.

---

## `useMemo`

The `useMemo` hook returns a memoized value. It re-executes a function to compute a new value only when one of the dependencies has changed. This is useful for avoiding expensive calculations on every render.

### Syntax

```javascript
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

### Parameters

| Parameter | Type       | Description                                                                                              |
| :-------- | :--------- | :------------------------------------------------------------------------------------------------------- |
| `create`  | `function` | The function that returns the value to be memoized. This function should be pure.                        |
| `deps`    | `Array`    | An array of dependencies. The `create` function will be re-executed if any of these dependencies change. |

### Example

If you have a component that needs to filter a large list, this operation can be slow. Using `useMemo` ensures the filtering only happens when the list or the filter criteria changes.

```javascript
import React, { useState, useMemo } from 'react';

function ProductList({ products, filterTerm }) {
  // This expensive filtering operation will only re-run if
  // 'products' or 'filterTerm' changes.
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
Without `useMemo`, `products.filter` would run every time `ProductList` re-renders for any reason, potentially slowing down the UI. With `useMemo`, the expensive work is skipped if the inputs are the same.

---

## `useTransition`

The `useTransition` hook allows you to update state without blocking the UI. It returns a stateful value for the pending state of the transition and a function to start it.

This is ideal for situations where a state update can cause a noticeable lag, such as filtering a large dataset in response to user input.

### Syntax

```javascript
const [isPending, startTransition] = useTransition();
```

### Return Value

`useTransition` returns an array with two items:

| Item              | Type       | Description                                                                                                   |
| :---------------- | :--------- | :------------------------------------------------------------------------------------------------------------ |
| `isPending`       | `boolean`  | A flag that is `true` while the transition is pending. You can use this to show a loading indicator.          |
| `startTransition` | `function` | A function that takes a callback. Any state updates inside this callback are marked as non-urgent transitions. |

### Example

In this example, typing into the input field updates two pieces of state: the input value (urgent) and the filtered list (non-urgent). By wrapping the list update in `startTransition`, we ensure the input field remains responsive even if filtering the list is slow.

```javascript
import React, { useState, useTransition } from 'react';

const largeList = Array.from({ length: 10000 }, (_, i) => `Item ${i + 1}`);

function App() {
  const [isPending, startTransition] = useTransition();
  const [inputValue, setInputValue] = useState('');
  const [filteredList, setFilteredList] = useState(largeList);

  const handleChange = (e) => {
    // Urgent update: show what the user is typing immediately
    setInputValue(e.target.value);

    // Non-urgent update: wrap in startTransition
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

The `useDeferredValue` hook accepts a value and returns a new copy of the value that will defer to more urgent updates. If the current render is the result of an urgent update (like user input), React will return the previous value and then render the new value after the urgent render has completed.

This hook is similar to `useTransition` but is often easier to use when you don't have direct control over the state-setting call.

### Syntax

```javascript
const deferredValue = useDeferredValue(value, initialValue);
```

### Parameters

| Parameter      | Type  | Description                                                                                             |
| :------------- | :---- | :------------------------------------------------------------------------------------------------------ |
| `value`        | `any` | The value you want to defer.                                                                              |
| `initialValue` | `any` | (Optional) The initial value to be used until the first deferred value is available.                    |

### Example

This example creates a `ProductList` that receives a search query as a prop. By using `useDeferredValue` on the query, the list can re-render with the deferred query, preventing the main input field from lagging.

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
Here, as the user types, the `query` state updates immediately, keeping the input responsive. The `ProductList` component receives this new `query` but `useDeferredValue` tells React it can continue showing the old list (based on the previous `deferredQuery`) while it renders the new list in the background.

---

With these hooks, you can effectively manage complex rendering logic and ensure a smooth user experience. For more advanced patterns related to transitions, see the [Transitions](./advanced-transitions.md) guide.
