# Performance Hooks

React's rendering system is generally fast, but for complex applications, you may encounter performance bottlenecks. Performance Hooks provide tools to optimize your components by memoizing expensive calculations, preventing unnecessary re-renders, and scheduling non-urgent UI updates without blocking user interaction.

These hooks are powerful but should be used judiciously. Premature optimization can lead to more complex code. It's often best to first build your components without them and then use tools like the React Profiler to identify areas where optimization is genuinely needed.

This section covers the primary hooks designed for performance tuning:

<x-cards data-columns="2">
  <x-card data-title="useCallback" data-icon="lucide:save">
    Memoizes callback functions, preventing them from being re-created on every render.
  </x-card>
  <x-card data-title="useMemo" data-icon="lucide:calculator">
    Memoizes the result of an expensive calculation, re-computing it only when dependencies change.
  </x-card>
  <x-card data-title="useTransition" data-icon="lucide:fast-forward">
    Marks state updates as non-urgent, allowing other updates to render first without blocking the UI.
  </x-card>
  <x-card data-title="useDeferredValue" data-icon="lucide:hourglass">
    Defers the update of a non-critical part of the UI, keeping the application responsive.
  </x-card>
</x-cards>

## useCallback

`useCallback` returns a memoized version of a callback function. This is useful when passing callbacks to optimized child components that rely on reference equality to prevent unnecessary renders.

### Syntax

```javascript
const memoizedCallback = useCallback(callback, deps);
```

### Parameters

| Parameter  | Type      | Description                                                                                                                                                              |
| ---------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `callback` | `function`| The function to be memoized.                                                                                                                                             |
| `deps`     | `Array`   | An array of dependencies. The memoized callback will only be re-created if a value in this array changes. If omitted, a new function is returned on every render. |

### Example

Consider a `ProductPage` that passes a `handleSubmit` function to a `ShippingForm` component. If `ShippingForm` is wrapped in `React.memo`, using `useCallback` for `handleSubmit` prevents the form from re-rendering every time `ProductPage` re-renders for other reasons.

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
In this example, toggling the theme will not cause `ShippingForm` to re-render because the `handleSubmit` function reference remains stable.

## useMemo

`useMemo` returns a memoized value. It re-computes the value only when one of its dependencies has changed, which is useful for avoiding expensive calculations on every render.

### Syntax

```javascript
const memoizedValue = useMemo(create, deps);
```

### Parameters

| Parameter | Type       | Description                                                                                                                                                  |
| --------- | ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `create`  | `function` | A function that computes and returns the value to be memoized.                                                                                               |
| `deps`    | `Array`    | An array of dependencies. The `create` function will only be re-executed if a value in this array changes. If omitted, the value is recomputed on every render. |

### Example

If you have a component that needs to filter a large list of items, you can use `useMemo` to ensure the filtering operation only runs when the list or the filter text changes.

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
Here, `visibleTodos` is only recalculated when the `todos` array or the `filter` string changes, not on other component re-renders.

## useTransition

`useTransition` is a hook that lets you update state without blocking the UI. It returns a pending state and a function to wrap state updates that could be disruptive.

### Syntax

```javascript
const [isPending, startTransition] = useTransition();
```

### Returns

A tuple with two values:

| Value             | Type       | Description                                                                                             |
| ----------------- | ---------- | ------------------------------------------------------------------------------------------------------- |
| `isPending`       | `boolean`  | A boolean that is `true` if a transition is currently active. You can use this to show a loading state. |
| `startTransition` | `function` | A function that takes a callback. State updates inside this callback are marked as non-urgent transitions. |

### Example

When a user types into a search field, you might want to keep the input responsive while the search results are being filtered. `useTransition` can mark the state update for the search results as a lower-priority transition.

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
In this example, the text input remains fluid and responsive, even if rendering `SearchResults` is slow. The UI shows a "Loading..." message while the transition is pending.

For more advanced use cases, see the [Transitions](./advanced-transitions.md) guide.

## useDeferredValue

`useDeferredValue` accepts a value and returns a new copy of the value that will defer to more urgent updates. It's similar to `useTransition` but is often simpler for cases where you don't have direct access to the state-setting function.

### Syntax

```javascript
const deferredValue = useDeferredValue(value);
```

### Parameters

| Parameter | Type  | Description                                                                                                                                     |
| --------- | ----- | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| `value`   | `any` | The value to be deferred. React will initially render with the old value and then attempt a re-render with the new value after urgent updates. |

### Example

This hook can be used to implement the same search-as-you-type behavior as `useTransition` but in a different way. It's particularly useful when the value comes from props or another hook.

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
Here, the `SearchResults` component receives the `query` immediately but uses `useDeferredValue` to create a `deferredQuery`. While the component waits for the new query to be rendered, the `deferredQuery` holds the previous value, allowing the UI to remain responsive. The `isStale` flag can be used to provide visual feedback, like dimming the old results.

---

After mastering these performance optimizations, you can explore other specialized hooks. For more information, proceed to the [Other Hooks](./hooks-other.md) guide.