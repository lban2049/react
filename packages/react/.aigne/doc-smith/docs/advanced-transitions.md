# Transitions

Transitions in React help you manage complex UI updates without blocking the main thread, ensuring your application remains responsive to user input. By marking certain state updates as "transitions," you tell React that they are not urgent and can be interrupted if a more critical update, like a key press or a click, comes in.

This is primarily achieved through two main APIs: the `useTransition` Hook for components and the standalone `startTransition` function.

## The `useTransition` Hook

The `useTransition` hook is the standard way to use transitions within your function components. It provides a stateful value for the pending status of the transition and a function to start it.

For a detailed guide on performance-related hooks, see the [Performance Hooks](./hooks-performance.md) documentation.

### Usage

Calling `useTransition` returns an array with two items:

1.  `isPending` (boolean): A flag that is `true` while the transition is active. You can use this to show loading indicators or other pending UI states.
2.  `startTransition` (function): A function that you wrap around a state update to mark it as a transition.

```javascript
const [isPending, startTransition] = useTransition();
```

### Example: Filtering a List

Imagine you have an input field that filters a large list of items. Without a transition, typing quickly could cause the UI to lag as it tries to re-render the list on every keystroke. With `useTransition`, you can keep the input field responsive while the list updates in the background.

```jsx
import { useState, useTransition } from 'react';

function App() {
  const [isPending, startTransition] = useTransition();
  const [inputValue, setInputValue] = useState('');
  const [filterTerm, setFilterTerm] = useState('');

  const handleInputChange = (e) => {
    // Update the input field immediately - this is an urgent update.
    setInputValue(e.target.value);

    // Wrap the list filtering logic in a transition - this is a non-urgent update.
    startTransition(() => {
      setFilterTerm(e.target.value);
    });
  };

  return (
    <div>
      <input type="text" value={inputValue} onChange={handleInputChange} />
      {isPending && <p>Updating list...</p>}
      {/* A component that renders a list based on `filterTerm` */}
      {/* <FilteredList term={filterTerm} /> */}
    </div>
  );
}
```

In this example, the `inputValue` state updates immediately, so the user sees their typing without delay. The `filterTerm` state, which might trigger an expensive re-render, is updated inside `startTransition`. React will handle this update with a lower priority, and the `isPending` flag will be true until the transition completes.

## The `startTransition` Function

React also exports a standalone `startTransition` function that can be used when the `useTransition` hook is not available, such as in data libraries or outside of React components.

### Usage

You import `startTransition` directly from React and pass it a callback function containing the non-urgent state updates.

```javascript
import { startTransition } from 'react';

// Some event handler or data-fetching logic
function handleUpdate() {
  // State updates inside this callback are marked as non-urgent.
  startTransition(() => {
    // e.g., setSomeState(newValue);
  });
}
```

### Parameters

| Name      | Type         | Description                                                                                                                                                             |
| :-------- | :----------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `scope`   | `() => void` | A function that contains one or more state updates (e.g., `setState`). React immediately calls this function and treats any updates within it as transitions.          |
| `options` | `object`     | *(Optional)* An object with options. If the `enableTransitionTracing` feature flag is on, you can provide a `name` for the transition, which helps in debugging with React DevTools. |

## Conceptual Model

Transitions allow React to differentiate between urgent and non-urgent updates. This ensures that user interactions that require immediate feedback are not delayed by slower, background rendering tasks.

```d2
direction: down

"User-Interaction": {
  shape: person
  label: "User Interaction"
}

"High-Priority-Update": {
  shape: rectangle
  label: "Urgent Update\n(e.g., typing in input)"
  style.stroke: "#ff4d4f"
}

"Low-Priority-Update": {
  shape: rectangle
  label: "Transition Update\n(e.g., updating list)"
  style.stroke: "#faad14"
}

"UI-Render": {
  shape: rectangle
  label: "UI Render"
  style.fill: "#f6ffed"
}

"User-Interaction" -> "High-Priority-Update": "Triggers"
"High-Priority-Update" -> "UI-Render": "High Priority\nCannot be interrupted"

"User-Interaction" -> "Low-Priority-Update": "Triggers"
"Low-Priority-Update" -> "UI-Render": "Low Priority\nCan be interrupted by urgent updates"
```

## Experimental Transition APIs

While `useTransition` and `startTransition` are the primary stable APIs, the React team is exploring more advanced capabilities related to transitions. These APIs are unstable and their behavior may change.

-   **`unstable_addTransitionType`**: This function allows associating a specific type (a string) with a currently active transition. This is intended to be used with features like View Transitions to apply different animations or behaviors based on the type of transition occurring.
-   **`unstable_startGestureTransition`**: This function is designed for transitions that are tied to continuous user gestures, like dragging or swiping. It requires a `GestureProvider` to manage the lifecycle of the gesture-based transition.

These are available for experimentation but should not be used in production environments. For more on this, please refer to the [Experimental APIs](./advanced-experimental.md) guide.

---

By leveraging transitions, you can build more complex and responsive user interfaces that handle data-intensive updates gracefully. To learn about other ways to optimize rendering, continue to the [Performance Hooks](./hooks-performance.md) section.