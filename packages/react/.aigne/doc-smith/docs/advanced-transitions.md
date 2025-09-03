# Transitions

In React, transitions are a mechanism for managing UI updates without blocking user interaction. They allow you to designate certain state updates as "transitions," which tells React that they can be interrupted if more urgent updates, like user input, occur. This ensures the application remains responsive even during complex rendering tasks.

This is particularly useful for updates that may be slow, such as filtering a large list or fetching data that results in a significant visual change. The core idea is to separate urgent updates (e.g., typing into a search box) from non-urgent, or transitional, updates (e.g., displaying the search results).

React provides two primary APIs for working with transitions:
- **`useTransition`**: The preferred hook for managing transitions within function components. You can find detailed documentation on our page for [Performance Hooks](./hooks-performance.md).
- **`startTransition`**: A standalone function that can be used when the `useTransition` hook is not available, such as in data libraries or outside of components.

## `startTransition`

The `startTransition` function allows you to wrap a state update, marking it as non-urgent.

```javascript
import { startTransition } from 'react';

// Any state updates inside the startTransition callback are treated as non-urgent.
startTransition(() => {
  // e.g., set state for a slow-rendering component
  setFilteredList(data);
});
```

### How it Works

This diagram illustrates the flow of concurrent updates using transitions:

```d2
direction: down

"User Interaction": {
  shape: person
}

"Urgent Update": {
  shape: rectangle
  label: "Urgent State Update (e.g., input value)"
  style.fill: "#e6f7ff"
}

"Transition Update": {
  shape: rectangle
  label: "Transition State Update (e.g., search results)"
  style.fill: "#fffbe6"
}

"Immediate Render": {
  shape: rectangle
  label: "Immediate, uninterruptible render"
  style.fill: "#f6ffed"
}

"Interruptible Render": {
  shape: rectangle
  label: "Interruptible render"
  style.fill: "#fff0f6"
}

"User Interaction" -> "Urgent Update"
"User Interaction" -> "Transition Update"

"Urgent Update" -> "Immediate Render": "Triggers"
"Transition Update" -> "Interruptible Render": "Triggers"

"Immediate Render" -> "Interruptible Render": "Can interrupt"

```

When a state update is wrapped in `startTransition`, React can continue to process user input while rendering the update in the background. If the user provides new input, React can interrupt the transition's render, process the new urgent update, and then restart the transition render with the latest data.

### Parameters

`startTransition` accepts a callback function and an optional options object.

| Name      | Type           | Description                                                                                              | Required |
|-----------|----------------|----------------------------------------------------------------------------------------------------------|----------|
| `scope`   | `() => void`   | A function that executes one or more state updates. These updates will be marked as transitions.       | Yes      |
| `options` | `object`       | An optional object for configuration. Currently, it only supports a `name` for debugging purposes.       | No       |

- **`options.name`**: A string used by React DevTools to help identify and trace the transition.

### Example

Consider a search input that filters a large list. We want the input field to feel immediate, while the list filtering can be deferred.

```javascript
import { startTransition } from 'react';

// Urgent update: Update the text in the input field
setSearchQuery(input);

// Non-urgent update: Wrap the list filtering in a transition
startTransition(() => {
  setFilteredList(getFilteredItems(input));
});
```

In this scenario, even if `getFilteredItems` is a slow operation, the user can continue typing in the search box without any lag because the `setSearchQuery` update is handled with high priority.

## Experimental Transition APIs

React also includes experimental APIs related to transitions that are subject to change. These are intended for advanced use cases and library authors.

<x-cards data-columns="2">
  <x-card data-title="unstable_startGestureTransition" data-icon="lucide:move-3d">
    Used for creating transitions tied to gesture-based interactions, like swiping. It requires a `GestureProvider` to manage the lifecycle of the gesture.
  </x-card>
  <x-card data-title="unstable_addTransitionType" data-icon="lucide:layers">
    Allows adding a descriptive type to an ongoing transition, such as 'fade' or 'slide'. This is part of the experimental View Transition support and helps coordinate animations.
  </x-card>
</x-cards>

These APIs are enabled by feature flags (`enableGestureTransition`, `enableViewTransition`) and are not intended for general application use at this time.

## Summary

Transitions are a key feature for building complex, yet highly responsive, user interfaces in React. By separating urgent updates from non-urgent ones, you can prevent your application from becoming sluggish during heavy rendering tasks.

For most component-level use cases, the `useTransition` hook is the ideal tool. For more advanced scenarios or work outside of components, `startTransition` provides the necessary control.

To further optimize your component's performance, explore other built-in hooks.

<x-card data-title="Performance Hooks" data-icon="lucide:gauge-circle" data-href="/hooks/performance" data-cta="Read More">
  Learn about other performance-related hooks like useTransition and useDeferredValue to optimize your application's rendering.
</x-card>