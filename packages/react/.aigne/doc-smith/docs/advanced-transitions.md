# Transitions

Transitions in React are a core feature for managing complex UI updates without blocking the main thread. They allow you to mark certain state updates as non-urgent, enabling React to keep the user interface responsive even during demanding rendering tasks. This is particularly useful for updates that might be slow, such as filtering a large list or re-rendering a complex visualization.

This guide explores the concept of transitions and the primary APIs for implementing them: the `startTransition` function and the `useTransition` hook.

For a focused guide on the hook itself, you can also refer to the [Performance Hooks](./hooks-performance.md) documentation.

## The Core Problem: Urgent vs. Non-Urgent Updates

By default, React treats all state updates as urgent. When a user interacts with an application (e.g., typing into an input), React will attempt to re-render the component tree immediately. If this re-render is computationally expensive, it can block the main thread, causing the UI to freeze or lag. This results in a poor user experience.

Transitions solve this by allowing you to separate updates into two categories:

1.  **Urgent Updates**: Direct user interactions that require immediate feedback, like typing, clicking, or pressing a key.
2.  **Transition Updates**: UI changes that don't need to be shown instantly, such as displaying search results or filtering data.

When you wrap an update in a transition, you are telling React that it's okay to delay this render and, more importantly, to interrupt it if a more urgent update comes in.

The following diagram illustrates the difference between a standard blocking update and one handled with a transition.

```d2
shape: sequence_diagram

User: User
UI: UI Thread
React: React Renderer

subgraph "Standard Blocking Update" {
  direction: right
  User -> UI: "Types in search input"
  UI -> React: "setState for input & list"
  React: "Renders entire component (can be slow)" {style: {fill: "#ffcaca"}}
  UI <- React: "Render complete"
  note right of UI: "UI feels laggy as typing is delayed by list filtering."
}

subgraph "Update with Transition" {
  direction: right
  User -> UI: "Types in search input"
  UI -> React: "Urgent setState (input value)"
  UI <- React: "Input field updates instantly"
  UI -> React: "startTransition(setState for list)"
  React: "Begins non-urgent render for list" {style: {fill: "#caffca"}}
  User -> UI: "Types again"
  UI -> React: "New urgent setState"
  React: "Interrupts list render, handles new input update first" {style: {stroke-dash: 2}}
  UI <- React: "Input updates instantly again"
}
```

## `startTransition`

The `startTransition` function allows you to mark state updates inside a callback as transitions. It can be used anywhere, including outside of components (e.g., in data libraries).

### Syntax

```javascript
import { startTransition } from 'react';

startTransition(scope, options?);
```

### Parameters

| Name      | Type       | Description                                                                                                                                              |
| :-------- | :--------- | :------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `scope`   | `() => void` | A function that contains the state update(s) you want to mark as a transition. React will immediately call this function with no arguments.            |
| `options` | `object`   | *(Optional)* An object containing configuration for the transition. Currently, it only supports a `name` for React DevTools profiling. |

### Example

Here's how to use `startTransition` to prevent a slow data filter from blocking user input.

```javascript
import { startTransition } from 'react';

// Assume setInputValue and setSearchQuery are from useState
function handleSearch(e) {
  // Urgent: show what the user is typing
  setInputValue(e.target.value);

  // Non-urgent: show the filtered results
  startTransition(() => {
    setSearchQuery(e.target.value);
  });
}
```

In this example, the input field will update immediately as the user types. The `setSearchQuery` update, which might trigger an expensive re-render of a list, is wrapped in `startTransition`. This tells React it can be deferred, ensuring the input remains responsive.

## `useTransition` Hook

For use inside function components, the `useTransition` hook is the recommended approach. It provides the same functionality as `startTransition` but also includes a pending state indicator, which is useful for providing feedback to the user.

### Syntax

The hook returns an array with two values:

```javascript
import { useTransition } from 'react';

const [isPending, startTransition] = useTransition();
```

### Return Values

| Name             | Type          | Description                                                                                             |
| :--------------- | :------------ | :------------------------------------------------------------------------------------------------------ |
| `isPending`      | `boolean`     | A boolean value that is `true` if a transition is currently active and pending, and `false` otherwise.        |
| `startTransition`| `() => void`  | The function to wrap your non-urgent state update(s).                                                     |

### Example with Pending State

Building on the previous example, we can use `isPending` to show a loading indicator while the list is being filtered.

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
        {/* List of filteredItems */}
        <ul>
          {filteredItems.map(item => <li key={item}>{item}</li>)}
        </ul>
      </div>
    </>
  );
}
```

In this component, the list becomes semi-transparent while React is busy rendering the new filtered items in the background, providing visual feedback that the update is in progress.

## Advanced and Experimental Features

The transition mechanism is extensible and supports more advanced, often experimental, use cases.

<x-cards data-columns="2">
  <x-card data-title="View Transitions" data-icon="lucide:gallery-vertical">
    The `unstable_addTransitionType` function allows a transition to be associated with a specific type, which is used by other features like `unstable_ViewTransition` to create animated transitions between different DOM states.
  </x-card>
  <x-card data-title="Gesture Transitions" data-icon="lucide:move">
    The `unstable_startGestureTransition` function is designed for handling transitions initiated by user gestures (e.g., swiping). It helps create smoother UI updates that are tightly coupled to the gesture itself.
  </x-card>
</x-cards>

These APIs are unstable and their usage is subject to change. They are mentioned here to provide a complete picture of the capabilities of React's transition system.

## Summary

Transitions are a key feature for building performant and responsive React applications. By distinguishing between urgent and non-urgent updates, you can ensure that your application's UI never feels stuck, even when performing heavy rendering work.

- Use **`useTransition`** in components to get a pending state for user feedback.
- Use **`startTransition`** for updates outside of components or when a pending state is not needed.

### Next Steps

Now that you understand transitions, you may want to explore related performance features or dive into other advanced topics.

<x-cards>
  <x-card data-title="Performance Hooks" data-icon="lucide:gauge-circle" data-href="/hooks/performance">
    Learn about other performance-related hooks like `useDeferredValue`, which is closely related to `useTransition`.
  </x-card>
  <x-card data-title="Experimental APIs" data-icon="lucide:flask-conical" data-href="/advanced/experimental">
    Explore other experimental and unstable APIs in React to get a glimpse of future capabilities.
  </x-card>
</x-cards>