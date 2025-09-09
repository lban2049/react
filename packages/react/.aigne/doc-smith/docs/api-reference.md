# API Reference

This page provides a comprehensive reference for all public APIs exported from the `react` package. It covers built-in components, Hooks, and various utilities for both client and server environments. For more in-depth explanations and usage examples, please refer to the specific guides in the [Core APIs](./core-apis.md), [Hooks](./hooks.md), and [Advanced Guides](./advanced.md) sections.

## Components

These are the built-in components you can use in your JSX.

| Component | Description |
|---|---|
| `Component` | The base class for React components defined as ES6 classes. |
| `PureComponent` | Similar to `Component` but implements `shouldComponentUpdate()` with a shallow prop and state comparison. |
| `Fragment` | Lets you group a list of children without adding extra nodes to the DOM. Can be written as `<>...</>`. |
| `Profiler` | Measures rendering performance of a React tree for performance optimization. |
| `StrictMode` | A tool for highlighting potential problems in an application. It activates additional checks and warnings for its descendants. |
| `Suspense` | Lets you display a fallback UI until its children have finished loading. Used for code splitting and data fetching. |

## Hooks

Hooks let you use state and other React features in function components. They can be categorized by purpose, such as State, Effect, and Performance. For a detailed guide on each, visit the main [Hooks documentation](./hooks.md).

| Hook | Description |
|---|---|
| `useActionState` | A hook to manage the state of a form action. |
| `useCallback` | Returns a memoized callback function, useful for performance optimizations. |
| `useContext` | Accepts a context object (the value returned from `React.createContext`) and returns the current context value for that context. |
| `useDebugValue` | Can be used to display a label for custom hooks in React DevTools. |
| `useDeferredValue` | Defers updating a part of the UI, helping to keep the interface responsive during heavy computations. |
| `useEffect` | Accepts a function that contains imperative, possibly effectful code. It runs after render commits to the screen. |
| `useId` | A hook for generating unique IDs that are stable across the server and client. |
| `useImperativeHandle` | Customizes the instance value that is exposed to parent components when using `ref`. |
| `useInsertionEffect` | Similar to `useEffect`, but it fires synchronously *before* all DOM mutations. Useful for CSS-in-JS libraries. |
| `useLayoutEffect` | Fires synchronously after all DOM mutations. Use this to read layout from the DOM and synchronously re-render. |
| `useMemo` | Returns a memoized value, recomputing it only when one of the dependencies has changed. |
| `useOptimistic` | A hook that lets you optimistically update the UI, assuming a data submission will be successful. |
| `useReducer` | An alternative to `useState`. Accepts a reducer of type `(state, action) => newState`, and returns the current state paired with a `dispatch` method. |
| `useRef` | Returns a mutable ref object whose `.current` property is initialized to the passed argument. |
| `useState` | A hook that lets you add React state to function components. |
| `useSyncExternalStore` | A hook that lets you subscribe to an external store, ensuring UI consistency. |
| `useTransition` | A hook to mark state updates as non-urgent, preventing them from blocking user input. |
| `use` | A hook that lets you read the value of a resource like a Promise or context. |

## Utilities

These are top-level utilities for working with React elements and components.

| Utility | Description |
|---|---|
| `Children` | Provides utilities for dealing with the `props.children` opaque data structure. Includes `Children.map`, `Children.forEach`, etc. |
| `cloneElement` | Clones and returns a new React element using an element as the starting point. |
| `createElement` | Creates and returns a new React element of the given type. JSX compiles to this function call. |
| `isValidElement` | Verifies the object is a React element. Returns `true` or `false`. |
| `version` | A string containing the current version of React. |
| `act` | A testing utility that ensures all updates related to state changes are processed and applied to the DOM before making assertions. (DEV-only) |

## APIs

These top-level APIs are used to create contexts, refs, and higher-order components.

| API | Description |
|---|---|
| `createContext` | Creates a Context object. Components can subscribe to this context to read its value. |
| `createRef` | Creates a ref object that can be attached to React elements via the `ref` attribute. |
| `forwardRef` | Creates a React component that forwards the `ref` attribute it receives to another component below in the tree. |
| `lazy` | Lets you define a component that is loaded dynamically. Used for code-splitting. |
| `memo` | A higher-order component for memoizing a component's render output to prevent re-rendering if its props are the same. |
| `startTransition` | A function that lets you mark state updates as non-urgent transitions. |
| `cache` | A function to cache the result of a data fetch or computation. |

## Unstable & Experimental APIs

> **Warning**
> These APIs are not yet stable and may be changed or removed in a future release. Use them at your own risk in production applications.

| API | Description |
|---|---|
| `unstable_Activity` | A component for coordinating transitions on offscreen content. |
| `unstable_SuspenseList` | Helps coordinate the loading state for multiple `Suspense` components. |
| `unstable_LegacyHidden` | A component that hides its children from view but keeps their state. |
| `unstable_Scope` | An experimental component for creating scoped event systems. |
| `unstable_TracingMarker` | An experimental component for marking performance traces. |
| `unstable_ViewTransition` | An experimental component for coordinating view transitions. |
| `unstable_useCacheRefresh` | An experimental Hook to invalidate the React `cache`. |
| `unstable_getCacheForType` | An experimental function to access a cache instance for a given type. |
| `unstable_addTransitionType` | An experimental function to add metadata to transitions. |
| `experimental_useEffectEvent` | An experimental hook to extract non-reactive logic from `useEffect`. |