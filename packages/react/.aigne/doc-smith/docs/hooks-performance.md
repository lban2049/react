# Performance Hooks

React is generally fast out of the box, but certain interactions can become slow as your application grows. This can happen when dealing with expensive computations, or large component trees that re-render frequently. Performance Hooks are designed to help you optimize your components in these specific situations.

It's important to profile your application and identify performance bottlenecks before applying these optimizations. Used incorrectly, they can add unnecessary complexity. This guide covers the primary hooks for performance tuning: `useCallback`, `useMemo`, `useTransition`, and `useDeferredValue`.

For related topics, you might want to review [State Hooks](./hooks-state.md) and [Effect Hooks](./hooks-effect.md), as performance optimizations often involve managing state and side effects efficiently.

---

## `useCallback`

The `useCallback` hook memoizes a callback function, preventing it from being recreated on every render. This is particularly useful when passing callbacks to optimized child components that rely on reference equality to prevent unnecessary re-renders (e.g., components wrapped in `React.memo`).

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

| Parameter | Type | Description |
|---|---|---|
| `callback` | `function` | The function to be memoized. |
| `deps` | `Array` | An array of dependencies. The callback will only be re-created if one of these dependencies changes. |

### Returns

`useCallback` returns a memoized version of the callback function. This function's identity is guaranteed to be stable as long as the dependencies have not changed.

### Example

Consider a parent component that passes a handler to a memoized child component. Without `useCallback`, the child would re-render every time the parent does, because the handler function is a new object in memory.

```javascript
import React, { useState, useCallback } from 'react';

const Button = React.memo(({ onClick, children }) => {
  console.log(`Rendering button: ${children}`);
  return <button onClick={onClick}>{children}</button>;
});

function App() {
  const [count, setCount] = useState(0);
  const [otherState, setOtherState] = useState(false);

  // This function is recreated on every render of App,
  // causing the memoized Button to re-render unnecessarily.
  const handleIncrement = () => {
    setCount(c => c + 1);
  };

  // By using useCallback, this function reference is stable
  // as long as `setCount` doesn't change.
  const handleDecrement = useCallback(() => {
    setCount(c => c - 1);
  }, []); // Empty dependency array means the function is never recreated

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
In this example, clicking the "Toggle Other State" button will cause the `App` component to re-render. You will see "Rendering button: Increment (No useCallback)" in the console, but not for the decrement button, because its `onClick` prop is stable thanks to `useCallback`.

---

## `useMemo`

The `useMemo` hook memoizes a computed value. It runs a function and caches its result. On subsequent renders, it will return the cached result without re-executing the function, as long as the dependencies have not changed. This is useful for avoiding expensive calculations on every render.

### Syntax

```javascript
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

### Parameters

| Parameter | Type | Description |
|---|---|---|
| `create` | `function` | The function that computes the value to be memoized. |
| `deps` | `Array` | An array of dependencies. The value will only be recomputed if one of these dependencies changes. |

### Returns

`useMemo` returns the memoized value. This value will only be updated when a dependency changes.

### Example

Imagine a component that needs to filter a large list of items. This filtering can be slow. `useMemo` can prevent this expensive operation from running every time the component re-renders for other reasons.

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
  }, [filter]); // Only re-run when `filter` changes

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
Here, the expensive filtering logic is wrapped in `useMemo`. When you click the "Toggle Other State" button, the component re-renders, but "Filtering list..." is not logged to the console because the `filter` dependency has not changed. The calculation is skipped, and the cached `filteredList` is used instead.

---

## `useTransition`

The `useTransition` hook allows you to update state without blocking the UI. It's designed for situations where a state update can cause a noticeable lag, such as filtering a large dataset in response to user input. It marks certain state updates as "transitions," which tells React they can be interrupted if more urgent updates (like typing) come in.

### Syntax

```javascript
const [isPending, startTransition] = useTransition();
```

### Returns

`useTransition` returns an array with two elements:

| Value | Type | Description |
|---|---|---|
| `isPending` | `boolean` | A boolean that is `true` while the transition is pending. You can use this to show a loading indicator. |
| `startTransition` | `function` | A function to wrap your state update in. React will treat any state updates inside this wrapper as non-urgent. |

### Example

This example shows how `useTransition` can keep a text input responsive while a list is being filtered.

```javascript
import React, { useState, useTransition } from 'react';

// Assume List is a component that renders a large number of items
function List({ items }) { ... }

function App() {
  const [isPending, startTransition] = useTransition();
  const [query, setQuery] = useState('');
  const [filteredItems, setFilteredItems] = useState([]);

  const handleChange = (e) => {
    // Urgent: update the input field immediately
    setQuery(e.target.value);

    // Non-urgent: wrap the slow state update in a transition
    startTransition(() => {
      // This might be a slow operation
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
When the user types in the input, `setQuery` updates immediately, so the input field feels responsive. The `setFilteredItems` update inside `startTransition` runs in the background. While it's running, `isPending` is `true`, allowing you to show a loading state.

---

## `useDeferredValue`

`useDeferredValue` is similar to `useTransition` but serves a slightly different purpose. It lets you defer re-rendering for a non-urgent part of the UI. It accepts a value and returns a new copy of that value which will "lag behind" the original during urgent renders. This is useful when the value comes from a parent component and you don't control the state update itself.

### Syntax

```javascript
const deferredValue = useDeferredValue(value);
```

### Parameters

| Parameter | Type | Description |
|---|---|---|
| `value` | `any` | The value you want to defer. |

### Returns

Returns a deferred version of the value. During an initial render, the returned value will be the same as the value provided. During updates, React will first re-render with the old value and then attempt a re-render with the new value in the background.

### Example

Let's refactor the previous filtering example using `useDeferredValue`.

```javascript
import React, { useState, useDeferredValue, useMemo } from 'react';

// Assume SearchResults is a component that renders a large list
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
In this setup, the `App` component updates the `query` state immediately. However, `deferredQuery` will lag behind. React will update the `SearchResults` component with the `deferredQuery` as a lower-priority update. This keeps the input responsive, and we can use the `isStale` flag to provide visual feedback (like reduced opacity) that the results are being updated.

### Which one to use?

- Use `useTransition` when you have access to the state setting function.
- Use `useDeferredValue` when you don't control the state update directly and only have the value that's causing the slow re-render.

---

### Summary

Performance Hooks are powerful tools for keeping your React application fast and responsive. By memoizing functions and values and deferring non-urgent UI updates, you can solve many common performance issues.

```d2
direction: down

start: "Is my component slow?"

check_cause: "What is the cause?" {
  shape: diamond
}

re_render: "Unnecessary re-renders of child components?"
expensive_calc: "Expensive calculation on every render?"
ui_block: "State update blocks UI interaction?"

use_callback: "useCallback to memoize functions passed as props."
use_memo: "useMemo to memoize the result of the calculation."
use_transition: "useTransition or useDeferredValue to defer the update."

start -> check_cause
check_cause -> re_render
check_cause -> expensive_calc
check_cause -> ui_block

re_render -> use_callback
expensive_calc -> use_memo
ui_block -> use_transition
```

After optimizing your components, you can continue to explore [Other Hooks](./hooks-other.md) or dive into more advanced patterns in the [Transitions](./advanced-transitions.md) guide.