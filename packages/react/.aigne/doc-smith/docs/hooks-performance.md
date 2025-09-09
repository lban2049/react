# Performance Hooks

Optimizing performance is a critical aspect of building responsive and fluid user interfaces. React provides a set of specialized Hooks designed to help you prevent unnecessary computations and renders, ensuring your application remains fast even as it scales. These Hooks are powerful tools for memoization and managing non-urgent UI updates.

This guide covers the primary performance Hooks. For Hooks related to state management, see [State Hooks](./hooks-state.md), and for side effects, refer to [Effect Hooks](./hooks-effect.md).

## useCallback

The `useCallback` Hook memoizes a callback function, preventing it from being recreated on every render. This is particularly useful when passing callbacks to optimized child components that rely on reference equality to prevent unnecessary re-renders.

`useCallback` will return a memoized version of the callback that only changes if one of the dependencies has changed.

### Syntax

```javascript React Hook: useCallback icon=logos:react
const memoizedCallback = useCallback(
  () => {
    doSomething(a, b);
  },
  [a, b],
);
```

### Parameters

| Parameter | Type | Description |
|---|---|---|
| `callback` | `function` | The function to memoize. |
| `deps` | `Array<any>` | A list of dependencies. The callback will only be re-created if a value in this array changes. |

### Example

Consider a child component wrapped in `React.memo`. If the parent passes a new function instance on every render, the child will re-render unnecessarily. `useCallback` solves this.

```javascript ParentComponent.js icon=logos:react
import React, { useState, useCallback } from 'react';
import MemoizedChild from './MemoizedChild';

function ParentComponent() {
  const [count, setCount] = useState(0);
  const [otherState, setOtherState] = useState(false);

  // This function will only be recreated if `count` changes.
  const handleClick = useCallback(() => {
    console.log(`Button clicked! Count is ${count}`);
  }, [count]);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment Count</button>
      <button onClick={() => setOtherState(!otherState)}>Toggle Other State</button>
      {/* handleClick function identity is stable across re-renders unless count changes */}
      <MemoizedChild onClick={handleClick} />
    </div>
  );
}
```

In this example, clicking "Toggle Other State" re-renders `ParentComponent` but does not recreate `handleClick`. Therefore, `MemoizedChild` does not re-render because its `onClick` prop has not changed.

## useMemo

Similar to `useCallback`, `useMemo` is for memoization, but instead of memoizing a function, it memoizes the *result* of a function call. It's ideal for avoiding expensive calculations on every render.

`useMemo` will only recompute the memoized value when one of the dependencies has changed.

### Syntax

```javascript React Hook: useMemo icon=logos:react
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

### Parameters

| Parameter | Type | Description |
|---|---|---|
| `create` | `() => T` | The function that returns the value to be memoized. |
| `deps` | `Array<any>` | A list of dependencies. The value will only be re-calculated if a dependency changes. |

### Example

If you have a component that needs to compute a value from a large dataset, `useMemo` can prevent this computation from running every time the component re-renders for other reasons.

```javascript DataDisplay.js icon=logos:react
import React, { useState, useMemo } from 'react';

function expensiveCalculation(num) {
  console.log('Performing expensive calculation...');
  // Imagine a heavy computation here
  let total = 0;
  for (let i = 0; i < 1000000000; i++) {
    total += num;
  }
  return total % 100;
}

function DataDisplay({ data }) {
  const [theme, setTheme] = useState('light');

  // expensiveCalculation will only run when `data.number` changes.
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

Here, toggling the theme will not trigger `expensiveCalculation` because its dependency, `data.number`, has not changed.

## useTransition

`useTransition` lets you update state without blocking the UI. It marks specific state updates as "transitions," which tells React they can be interrupted if more urgent updates (like typing into an input) arrive.

This Hook is ideal for keeping your application responsive during data-fetching or when rendering large component trees.

### Syntax

It returns an array with two items:

```javascript React Hook: useTransition icon=logos:react
const [isPending, startTransition] = useTransition();
```

### Return Value

| Item | Type | Description |
|---|---|---|
| `isPending` | `boolean` | A boolean flag that is `true` while the transition is pending. You can use this to show a loading indicator. |
| `startTransition` | `(callback: () => void) => void` | A function that allows you to mark a state update as a transition. |

### Example

When filtering a large list, wrapping the filter logic in `startTransition` keeps the input field responsive.

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
    // The input state updates immediately, but the list update is deferred.
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

`useDeferredValue` lets you defer re-rendering a non-urgent part of the UI. It is similar in purpose to `useTransition` but is often easier to use when you don't have direct control over the state-setting call.

The Hook returns a deferred version of the value that will "lag behind" the most recent value during urgent re-renders.

### Syntax

```javascript React Hook: useDeferredValue icon=logos:react
const deferredValue = useDeferredValue(value);
```

### Parameters

| Parameter | Type | Description |
|---|---|---|
| `value` | `T` | The value you want to defer. |
| `initialValue` | `T` | (Optional) The initial value to use before the first deferred value is computed. |

### Example

This example achieves the same result as the `useTransition` example but with a different approach. We defer the `filter` value itself before using it to compute the list.

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

As the user types, the `input` updates immediately with the `filter` state. However, the `deferredFilter` value updates with a delay, so the expensive list filtering and re-rendering don't block the user's input.

---

By leveraging these performance Hooks, you can build highly optimized and responsive React applications. For more tools in the React toolkit, continue to [Other Hooks](./hooks-other.md).