# API Reference

This page provides a comprehensive reference for all public APIs in the React library. It is organized by category to help you quickly find what you need. For more detailed explanations and usage examples, follow the links to the relevant documentation sections.

## Hooks

Hooks allow you to use state and other React features in function components. They must be called at the top level of your components.

For a detailed guide, see the [Hooks](./hooks.md) documentation.

| Hook | Description |
|---|---|
| [`use`](./hooks-other.md) | Reads the value from a resource, like a Promise or context. |
| [`useActionState`](./hooks-state.md) | Manages the state of a form action. |
| [`useCallback`](./hooks-performance.md) | Returns a memoized callback function. |
| [`useContext`](./hooks-other.md) | Accepts a context object and returns its current value. |
| [`useDebugValue`](./hooks-other.md) | Displays a label for custom hooks in React DevTools. |
| [`useDeferredValue`](./hooks-performance.md) | Defers updating a part of the UI. |
| [`useEffect`](./hooks-effect.md) | Lets you perform side effects in function components. |
| [`useId`](./hooks-other.md) | Generates unique IDs that are stable across server and client. |
| [`useImperativeHandle`](./hooks-ref.md) | Customizes the instance value that is exposed to parent components when using `ref`. |
| [`useInsertionEffect`](./hooks-effect.md) | Similar to `useEffect`, but it fires synchronously before all DOM mutations. |
| [`useLayoutEffect`](./hooks-effect.md) | Fires synchronously after all DOM mutations. |
| [`useMemo`](./hooks-performance.md) | Returns a memoized value. |
| [`useOptimistic`](./hooks-state.md) | Allows you to optimistically update the UI. |
| [`useReducer`](./hooks-state.md) | An alternative to `useState` for managing complex state logic. |
| [`useRef`](./hooks-ref.md) | Returns a mutable ref object. |
| [`useState`](./hooks-state.md) | Adds state to a function component. |
| [`useSyncExternalStore`](./hooks-other.md) | Lets you subscribe to an external store. |
| [`useTransition`](./hooks-performance.md) | Lets you update the state without blocking the UI. |

## Components

These are built-in components that you can use in your JSX.

| Component | Description |
|---|---|
| [`<Component>`](./core-apis-components-and-props.md) | The base class for React components when they are defined using ES6 classes. |
| [`<Fragment>`](./core-apis-components-and-props.md) | Lets you group a list of children without adding extra nodes to the DOM. |
| [`<Profiler>`](./core-apis-components-and-props.md) | Measures rendering performance of a React tree for optimization. |
| [`<PureComponent>`](./core-apis-components-and-props.md) | Similar to `Component` but implements `shouldComponentUpdate()` with a shallow prop and state comparison. |
| [`<StrictMode>`](./core-apis-components-and-props.md) | A tool for highlighting potential problems in an application. |
| [`<Suspense>`](./advanced-code-splitting.md) | Lets your components "wait" for something before they can render, showing a fallback UI. |

## APIs

Top-level APIs provided by the `React` object.

| API | Description |
|---|---|
| [`cache`](./advanced-caching.md) | A utility for memoizing data-fetching functions. |
| [`cloneElement`](./core-apis-creating-elements.md) | Clones and returns a new React element using an element as the starting point. |
| [`createContext`](./core-apis-context.md) | Creates a Context object. |
| [`createElement`](./core-apis-creating-elements.md) | Creates and returns a new React element of the given type. |
| [`createRef`](./core-apis-refs.md) | Creates a ref object that can be attached to React elements. |
| [`forwardRef`](./core-apis-refs.md) | Creates a React component that forwards the ref attribute it receives to another component below in the tree. |
| [`lazy`](./advanced-code-splitting.md) | Lets you define a component that is loaded dynamically. |
| [`memo`](./hooks-performance.md) | A higher-order component for memoizing a component's render output. |
| [`startTransition`](./advanced-transitions.md) | Lets you mark a state update as a non-urgent transition. |
| [`version`](./api-reference.md) | A string containing the current React version. |

## Utilities

Helper functions for working with React's data structures.

| Utility | Description |
|---|---|
| [`Children`](./core-apis-children-utilities.md) | An object with methods for dealing with the `props.children` data structure. |
| [`isValidElement`](./core-apis-creating-elements.md) | Verifies the object is a React element. Returns `true` or `false`. |

## Experimental APIs

These APIs are experimental and may change or be removed in a future release. They are intended for early adoption and feedback.

For more details, see the [Experimental APIs](./advanced-experimental.md) guide.

| API | Description |
|---|---|
| `experimental_useEffectEvent` | An experimental Hook for extracting non-reactive logic from `useEffect`. |
| `unstable_Activity` | A component for coordinating UI appearance and disappearance during transitions. |
| `unstable_SuspenseList` | Coordinates the loading state of multiple `Suspense` components. |
| `unstable_ViewTransition` | An experimental API for creating animated view transitions. |
| `unstable_getCacheForType` | Retrieves a cache instance for a given type, used with `cache`. |
| `unstable_useCacheRefresh` | Provides a function to invalidate the React cache. |
| `unstable_postpone` | An experimental function to postpone rendering a component tree. |
| `unstable_LegacyHidden` | A component to hide a subtree from the UI without unmounting it. |
