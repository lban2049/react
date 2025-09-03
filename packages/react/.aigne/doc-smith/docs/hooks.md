# Hooks

Hooks are functions that allow you to “hook into” React state and lifecycle features from function components. They let you use state, effects, context, and other React features without writing a class, leading to more readable and composable code.

Before using Hooks, you should be familiar with two fundamental rules:
1.  **Only Call Hooks at the Top Level:** Don't call Hooks inside loops, conditions, or nested functions.
2.  **Only Call Hooks from React Functions:** Call them from React function components or custom Hooks, not regular JavaScript functions.

For more details, see the official documentation on the [Rules of Hooks](https://react.dev/link/invalid-hook-call).

This section provides a detailed guide to all built-in Hooks, grouped by their purpose.

```d2
direction: down

"React Hooks": {
  shape: hexagon
  
  "Categories": {
    grid-columns: 3
    grid-gap: 50

    "State": {
      shape: package
      "useState"
      "useReducer"
      "useActionState"
      "useOptimistic"
    }

    "Effects": {
      shape: package
      "useEffect"
      "useLayoutEffect"
      "useInsertionEffect"
    }

    "Performance": {
      shape: package
      "useMemo"
      "useCallback"
      "useTransition"
    }
    
    "Refs": {
      shape: package
      "useRef"
      "useImperativeHandle"
    }

    "Context": {
      shape: package
      "useContext"
    }

    "Other": {
      shape: package
      "useId"
      "useSyncExternalStore"
      "useDebugValue"
    }
  }

  "React Hooks" -> "Categories": "Grouped by purpose"
}
```

## Hook Categories

Explore the different types of Hooks to handle various aspects of your component's logic.

<x-cards data-columns="2">
  <x-card data-title="State Hooks" data-href="/hooks/state" data-icon="lucide:database">
    Manage component's state with `useState`, `useReducer`, `useActionState`, and `useOptimistic`.
  </x-card>
  <x-card data-title="Effect Hooks" data-href="/hooks/effect" data-icon="lucide:zap">
    Perform side effects in your components with `useEffect`, `useLayoutEffect`, and `useInsertionEffect`.
  </x-card>
  <x-card data-title="Ref Hooks" data-href="/hooks/ref" data-icon="lucide:git-commit">
    Reference values that aren’t needed for rendering with `useRef` and `useImperativeHandle`.
  </x-card>
  <x-card data-title="Performance Hooks" data-href="/hooks/performance" data-icon="lucide:gauge">
    Optimize your components' performance with `useCallback`, `useMemo`, `useTransition`, and `useDeferredValue`.
  </x-card>
  <x-card data-title="Other Hooks" data-href="/hooks/other" data-icon="lucide:puzzle">
    Explore other Hooks like `useContext`, `useId`, `useDebugValue`, and `useSyncExternalStore`.
  </x-card>
</x-cards>

## Complete API Reference

Here is a complete list of all built-in Hooks available in React.

| Hook | Description |
|---|---|
| [`useState`](./hooks-state.md) | Manages state within a function component. |
| [`useEffect`](./hooks-effect.md) | Performs side effects after render. |
| [`useContext`](./hooks-other.md) | Subscribes to React context without introducing nesting. |
| [`useReducer`](./hooks-state.md) | An alternative to `useState` for managing complex state logic. |
| [`useCallback`](./hooks-performance.md) | Returns a memoized callback function. |
| [`useMemo`](./hooks-performance.md) | Returns a memoized value. |
| [`useRef`](./hooks-ref.md) | Returns a mutable ref object. |
| [`useImperativeHandle`](./hooks-ref.md) | Customizes the instance value that is exposed to parent components when using `ref`. |
| [`useLayoutEffect`](./hooks-effect.md) | Fires synchronously after all DOM mutations. |
| [`useInsertionEffect`](./hooks-effect.md) | Fires synchronously before all DOM mutations, for CSS-in-JS libraries. |
| [`useTransition`](./hooks-performance.md) | Lets you update the state without blocking the UI. |
| [`useDeferredValue`](./hooks-performance.md) | Defers updating a part of the UI. |
| [`useId`](./hooks-other.md) | Generates unique IDs that are stable across server and client. |
| [`useSyncExternalStore`](./hooks-other.md) | Lets you subscribe to an external store. |
| [`useDebugValue`](./hooks-other.md) | Displays a label for custom hooks in React DevTools. |
| [`useActionState`](./hooks-state.md) | Manages the state of a form action. |
| [`useOptimistic`](./hooks-state.md) | Manages optimistic UI updates. |
| `use` | Lets you read the value of a resource like a Promise or context. |

---

Hooks are the foundation for building modern, functional React applications. By mastering their use, you can create efficient, readable, and maintainable components.

To see how these Hooks are applied in more complex patterns and environments, continue to the [Advanced Guides](./advanced.md).
