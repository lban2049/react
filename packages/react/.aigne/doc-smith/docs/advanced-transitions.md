# Transitions

Transitions are a core feature in React for keeping your application responsive, even during complex and potentially slow UI updates. By marking certain updates as "transitions," you tell React that they are not urgent and can be interrupted if a more important update, like user input, comes in. This prevents the main thread from being blocked and avoids a janky user experience.

React provides two primary APIs for working with transitions: the `useTransition` Hook for use within components, and the `startTransition` function for use outside of components.

For a component-centric guide, you can also refer to the [Performance Hooks](./hooks-performance.md) documentation.

## The `useTransition` Hook

The `useTransition` hook is the most common way to handle transitions within your function components. It provides a stateful value to track the pending state of the transition and a function to start it.

It returns an array with two items:
1.  `isPending`: A boolean that is `true` while the transition is active. You can use this to display loading indicators or provide visual feedback to the user.
2.  `startTransition`: A function that takes a callback. You wrap your slow state update(s) inside this callback to mark them as a transition.

### Example: Filtering a Large List

Imagine you have an input field that filters a very long list of items. Without a transition, typing into the input could feel sluggish because each keystroke triggers an expensive re-render of the list. With `useTransition`, the input field remains responsive while the list updates in the background.

```javascript Filterable List with Transition icon=logos:react
import { useState, useTransition } from 'react';

// Generate a large list for demonstration purposes
const allItems = Array.from({ length: 10000 }, (_, i) => `Item #${i + 1}`);

function MyComponent() {
  const [isPending, startTransition] = useTransition();
  const [inputValue, setInputValue] = useState('');
  const [filteredItems, setFilteredItems] = useState(allItems);

  const handleChange = (e) => {
    // Update the input field state immediately - this is an urgent update
    setInputValue(e.target.value);

    // Wrap the expensive filtering logic in startTransition
    startTransition(() => {
      const filtered = allItems.filter((item) =>
        item.toLowerCase().includes(e.target.value.toLowerCase())
      );
      setFilteredItems(filtered);
    });
  };

  return (
    <div>
      <input
        type="text"
        value={inputValue}
        onChange={handleChange}
        placeholder="Search through 10,000 items..."
      />
      {isPending && <p>Updating list...</p>}
      <ul style={{ opacity: isPending ? 0.5 : 1 }}>
        {filteredItems.map((item) => (
          <li key={item}>{item}</li>
        ))}
      </ul>
    </div>
  );
}
```

In this example, the `setInputValue` update is considered urgent and executes immediately, so the user sees their typing without delay. The `startTransition` call wraps the expensive filtering and `setFilteredItems` update. React can now interrupt this rendering if the user types another character, ensuring the app feels fluid.

## The `startTransition` Function

Sometimes you need to trigger a non-urgent update from outside a component, such as in a data library, an external store, or a complex event handler. For these cases, you can import and use the standalone `startTransition` function.

It works similarly to the function returned by the `useTransition` hook, wrapping a callback that contains state updates.

### Example: Updating from a Data Source

```javascript Standalone startTransition icon=logos:javascript
import { startTransition } from 'react';

// Assume this function is part of a data fetching library
// and `updateReactState` is a function that calls a React state setter.
function fetchAndUpdate(data) {
  // The state update might cause a large re-render in the UI.
  // We wrap it in startTransition to prevent blocking.
  startTransition(() => {
    updateReactState(data);
  });
}
```

This allows you to leverage transitions for any state update, regardless of where it originates, keeping your UI architecture flexible and performant.

## How Transitions Work

Under the hood, transitions allow React to work on multiple state updates concurrently. When an update is wrapped in a transition, React understands it can be paused to handle more urgent tasks and resumed later. This mechanism is central to React's concurrent rendering capabilities.

```d2 Transition Interruption Flow
direction: down

User-Input: {
  shape: c4-person
  label: "User Input\n(e.g., typing)"
}

React-Scheduler: {
  label: "React Scheduler"
  shape: diamond
}

Urgent-Update: {
  label: "Urgent State Update\n(e.g., input field)"
}

Transition-Update: {
  label: "Transition Update\n(e.g., filtering list)"
}

UI-Render: {
  label: "UI Render"
}

User-Input -> React-Scheduler: "Triggers update"
React-Scheduler -> Urgent-Update: "Prioritizes urgent work"
React-Scheduler -- "Interrupts" --> Transition-Update
Urgent-Update -> UI-Render: "Renders immediately"
Transition-Update -> UI-Render: "Renders when idle"
```

## Experimental Transition APIs

While `useTransition` and `startTransition` are stable, the React team is exploring more advanced transition capabilities. The following APIs are considered experimental and should not be used in production without caution.

*   `unstable_startGestureTransition`: Designed for smoother UI updates that are tied to user gestures, like dragging or swiping. It requires a `GestureProvider` to function.
*   `unstable_addTransitionType`: Part of the experimental View Transitions feature, this function allows you to associate a specific type (a string) with a running transition, enabling more granular control over animations and behaviors.

---

Transitions are a powerful tool for building sophisticated, highly responsive user interfaces in React. By distinguishing between urgent and non-urgent updates, you can ensure a smooth experience for your users, even in data-intensive applications.

### Next Steps

Explore these related concepts to further enhance your application's performance and user experience.

<x-cards>
  <x-card data-title="useDeferredValue" data-icon="lucide:arrow-down-right" data-href="/hooks/performance">
    Learn about a related hook that helps defer rendering of non-urgent parts of the UI, which is particularly useful for values that come from props or other hooks.
  </x-card>
  <x-card data-title="Code Splitting with lazy and Suspense" data-icon="lucide:box-select" data-href="/advanced/code-splitting">
    Discover how to improve initial load times by splitting your code and using Suspense for loading states, which works seamlessly with transitions.
  </x-card>
</x-cards>