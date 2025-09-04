# Hooks

Hooks are functions that let you “hook into” React state and lifecycle features from function components. They allow you to use state and other React features without writing a class, making your components cleaner and more reusable.

Hooks can only be called at the top level of your function components or from your own custom Hooks. You cannot call them inside loops, conditions, or nested functions. This ensures that Hooks are called in the same order each time a component renders, which allows React to correctly preserve the state of Hooks between multiple `useState` and `useEffect` calls.

### How Hooks Work

At a high level, Hooks provide a direct API to the React features you already know, such as state, lifecycle, context, and refs. When you call a Hook like `useState`, you are telling React that your component needs to keep track of some state.

```d2
direction: down

"Function Component": {
  shape: rectangle
  style.fill: "#e6f7ff"

  "Hooks": {
    label: "Hook Calls\n(useState, useEffect, ...)"
    shape: rectangle
    style.fill: "#f6ffed"
  }
}

"React Core": {
  shape: package
  label: "React Core Features"
  style.fill: "#fff7e6"

  State: { shape: stored_data }
  Lifecycle: { shape: step }
  Context: { shape: stored_data }
}

"Function Component".Hooks -> "React Core": "Hooks into"
"React Core" -> "Function Component": "Provides State & Lifecycle"
```

### Exploring the Hooks

React provides a set of built-in Hooks to cover a wide range of use cases. They are grouped below by their primary purpose. Dive into each section to learn more about them.

<x-cards data-columns="2">
  <x-card data-title="State Hooks" data-icon="lucide:database" data-href="/hooks/state">
    Manage your component's local state. These Hooks allow your components to remember information like user input, server responses, or UI state.
  </x-card>
  <x-card data-title="Effect Hooks" data-icon="lucide:zap" data-href="/hooks/effect">
    Perform side effects in your components. Effects are used for data fetching, setting up a subscription, or manually changing the DOM.
  </x-card>
  <x-card data-title="Ref Hooks" data-icon="lucide:anchor" data-href="/hooks/ref">
    Reference values that aren’t needed for rendering. Useful for accessing DOM nodes directly or for keeping a mutable value around.
  </x-card>
  <x-card data-title="Performance Hooks" data-icon="lucide:gauge-circle" data-href="/hooks/performance">
    Optimize your components' performance by skipping expensive recalculations and managing UI updates without blocking the user.
  </x-card>
  <x-card data-title="Other Hooks" data-icon="lucide:puzzle" data-href="/hooks/other">
    A collection of Hooks for other specific use cases, such as reading context, generating unique IDs, or subscribing to external stores.
  </x-card>
</x-cards>

### Full API Reference

For a quick lookup, here is a list of all built-in Hooks available in React.

| Hook | Description |
| --- | --- |
| `useState` | Declares a state variable that you can update, causing a re-render. |
| `useReducer` | An alternative to `useState` for managing complex state logic. |
| `useEffect` | Lets you perform side effects in function components. |
| `useLayoutEffect` | Fires synchronously after all DOM mutations. Use this to read layout from the DOM and synchronously re-render. |
| `useInsertionEffect` | Allows inserting elements into the DOM before any layout effects fire. Primarily for CSS-in-JS libraries. |
| `useContext` | Accepts a context object and returns the current context value for that context. |
| `useRef` | Returns a mutable ref object whose `.current` property is initialized to the passed argument. |
| `useCallback` | Returns a memoized callback function. |
| `useMemo` | Returns a memoized value. |
| `useImperativeHandle` | Customizes the instance value that is exposed to parent components when using `ref`. |
| `useTransition` | Lets you update the state without blocking the UI. Returns a stateful value for the pending state of the transition, and a function to start it. |
| `useDeferredValue` | Lets you defer updating a part of the UI. |
| `useId` | A hook for generating unique IDs that are stable across the server and client. |
| `useSyncExternalStore` | A hook recommended for reading and subscribing from external data sources in a way that’s compatible with concurrent rendering features. |
| `useDebugValue` | Can be used to display a label for custom hooks in React DevTools. |
| `useActionState` | A hook to manage the state of a form action. |
| `useOptimistic` | A hook that lets you optimistically update the UI. |
| `use` | A hook that allows you to read the value of a resource like a Promise or context. |

### Next Steps

To begin, a great starting point is learning how to add and manage state within your components. This is the foundation for creating interactive user interfaces.

Next: [State Hooks](./hooks-state.md)
