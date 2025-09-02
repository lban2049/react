# Hooks

Hooks are functions that let you “hook into” React state and lifecycle features from function components. They allow you to use state, side effects, context, and other React features without writing a class. This makes your component logic more reusable, composable, and easier to test.

Before using Hooks, it's important to understand their rules. The most critical one is that **Hooks can only be called at the top level of a function component or a custom Hook**. This ensures that Hooks are called in the same order each time a component renders, which is how React preserves the state of Hooks between multiple calls. For more details, see the guide on [Rules of Hooks](https://react.dev/link/invalid-hook-call).

React provides a variety of built-in Hooks. To help you understand their purpose, we've grouped them into the following categories:

<x-cards data-columns="2">
  <x-card data-title="State Hooks" data-href="/hooks/state" data-icon="lucide:database">
    Manage your component's state, from simple values to complex logic and optimistic UI updates. Includes `useState`, `useReducer`, `useActionState`, and `useOptimistic`.
  </x-card>
  <x-card data-title="Effect Hooks" data-href="/hooks/effect" data-icon="lucide:zap">
    Perform side effects, such as fetching data, subscribing to events, or manually manipulating the DOM. Includes `useEffect`, `useLayoutEffect`, and `useInsertionEffect`.
  </x-card>
  <x-card data-title="Ref Hooks" data-href="/hooks/ref" data-icon="lucide:anchor">
    Access DOM nodes or persist values across renders without causing a re-render. Includes `useRef` and `useImperativeHandle`.
  </x-card>
  <x-card data-title="Performance Hooks" data-href="/hooks/performance" data-icon="lucide:rocket">
    Optimize your application's performance by memoizing values, skipping expensive re-renders, and managing non-urgent UI updates. Includes `useCallback`, `useMemo`, `useTransition`, and `useDeferredValue`.
  </x-card>
  <x-card data-title="Other Hooks" data-href="/hooks/other" data-icon="lucide:boxes">
    A collection of hooks for other specific use cases, like accessing context, generating unique IDs, and subscribing to external stores. Includes `useContext`, `useId`, and more.
  </x-card>
</x-cards>

## Complete List of Hooks

Here is a comprehensive list of all built-in Hooks available in React for quick reference.

| Hook | Description |
|---|---|
| `useState` | Declares a state variable that you can update directly. |
| `useReducer` | Manages complex state logic using a reducer function, similar to Redux. |
| `useContext` | Reads and subscribes to a context, allowing you to pass data deep into the component tree. |
| `useEffect` | Performs side effects after the component renders, such as data fetching or subscriptions. |
| `useLayoutEffect` | Fires synchronously after all DOM mutations. Useful for reading layout from the DOM. |
| `useInsertionEffect` | Allows CSS-in-JS libraries to inject styles before `useLayoutEffect` to avoid performance issues. |
| `useCallback` | Returns a memoized version of a callback function that only changes if a dependency has changed. |
| `useMemo` | Returns a memoized value, recomputing it only when one of the dependencies has changed. |
| `useRef` | Returns a mutable ref object whose `.current` property can hold a value across renders. |
| `useImperativeHandle` | Customizes the instance value that is exposed to parent components when using `ref`. |
| `useDebugValue` | Displays a label for custom hooks in React DevTools. |
| `useTransition` | Lets you update the state without blocking the UI, marking updates as non-urgent. |
| `useDeferredValue` | Defers updating a part of the UI, useful for keeping the interface responsive during heavy computation. |
| `useId` | Generates unique IDs that are stable across server and client, to avoid hydration mismatches. |
| `useSyncExternalStore` | Subscribes to an external store, ensuring your component re-renders on updates. |
| `use` | Reads the value of a resource like a Promise or context. |
| `useActionState` | Manages the state of a form action, including pending and response states. |
| `useOptimistic` | Manages optimistic UI updates, showing a temporary state while an async action completes. |

Hooks are a central part of modern React development. Understanding how and when to use each one is key to building efficient and maintainable applications.

To begin, explore the most fundamental category of Hooks. 

### Next Steps

Continue to the next section to learn about [State Hooks](./hooks-state.md).