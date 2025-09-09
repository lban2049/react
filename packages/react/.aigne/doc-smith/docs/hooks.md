# Hooks

Hooks are functions that let you use state and other React features in function components. They allow you to "hook into" React's state and lifecycle features from components without writing a class, making your code more reusable and easier to reason about.

Before using Hooks, it's essential to understand their two main rules:
1.  **Only Call Hooks at the Top Level:** Don't call Hooks inside loops, conditions, or nested functions.
2.  **Only Call Hooks from React Functions:** Call them from React function components and not from regular JavaScript functions.

For a deeper dive into these rules, see the official documentation on [Rules of Hooks](https://react.dev/link/invalid-hook-call).

This page provides an overview of the built-in Hooks, categorized by their purpose. Select a category to learn more about the specific Hooks within it.

<x-cards data-columns="2">
  <x-card data-title="State Hooks" data-href="/hooks/state" data-icon="lucide:database">
    Manage component's state with `useState`, `useReducer`, `useActionState`, and `useOptimistic`.
  </x-card>
  <x-card data-title="Effect Hooks" data-href="/hooks/effect" data-icon="lucide:zap">
    Perform side effects in your components with `useEffect`, `useLayoutEffect`, and `useInsertionEffect`.
  </x-card>
  <x-card data-title="Ref Hooks" data-href="/hooks/ref" data-icon="lucide:anchor">
    Reference values that aren’t needed for rendering with `useRef` and `useImperativeHandle`.
  </x-card>
  <x-card data-title="Performance Hooks" data-href="/hooks/performance" data-icon="lucide:gauge-circle">
    Optimize your components' performance with `useCallback`, `useMemo`, `useTransition`, and `useDeferredValue`.
  </x-card>
  <x-card data-title="Other Hooks" data-href="/hooks/other" data-icon="lucide:puzzle">
    Explore other Hooks like `useContext`, `useId`, `useDebugValue`, and `useSyncExternalStore`.
  </x-card>
</x-cards>

## API Quick Reference

Here is a complete list of all built-in Hooks available in React for quick reference.

| Hook | Description |
| --- | --- |
| `useState` | Declares a state variable that you can update directly. |
| `useReducer` | Declares a state variable with complex update logic managed by a reducer function. |
| `useActionState` | Manages the state of a form action, handling pending and error states. |
| `useOptimistic` | Manages optimistic UI updates that can be reverted. |
| `useEffect` | Connects a component to an external system and performs side effects after rendering. |
| `useLayoutEffect` | Fires synchronously after all DOM mutations, useful for measuring layout. |
| `useInsertionEffect` | Fires synchronously before DOM mutations, primarily for CSS-in-JS libraries. |
| `useRef` | References a value that’s not needed for rendering, often used to access DOM elements. |
| `useImperativeHandle` | Customizes the ref handle exposed to parent components. |
| `useCallback` | Caches a function definition between re-renders to optimize performance. |
| `useMemo` | Caches the result of a calculation between re-renders. |
| `useTransition` | Marks a state update as a non-blocking transition to avoid freezing the UI. |
| `useDeferredValue` | Defers updating a non-critical part of the UI. |
| `useContext` | Reads and subscribes to a context, allowing data to be passed down the component tree. |
| `useId` | Generates a unique ID that is stable across server and client rendering. |
| `useDebugValue` | Displays a label for custom hooks in React DevTools. |
| `useSyncExternalStore` | Subscribes a component to an external data store. |
| `use` | Reads the value of a resource, like a Promise or context. |


This overview provides a map to the world of React Hooks. Each category contains powerful tools for building modern React applications. To get started, we recommend diving into the [State Hooks](./hooks-state.md) guide, as they are the foundation for interactivity in your components.