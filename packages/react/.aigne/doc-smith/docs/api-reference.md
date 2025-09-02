# API Reference

This page provides a comprehensive, A-Z reference for all public APIs exported from the `react` package. It's designed for quick lookups of function signatures, parameters, and return values. 

For more detailed explanations, usage patterns, and practical examples, please refer to the main documentation sections, such as [Core APIs](./core-apis.md) and [Hooks](./hooks.md).

## Components & Fragments

These are the core building blocks for creating user interfaces in React. They include base classes and special components for structuring your UI.

| API | Description |
|---|---|
| `Component` | The base class for React components defined using ES6 classes. | 
| `PureComponent` | Similar to `Component`, but it performs a shallow comparison of props and state to prevent unnecessary re-renders. | 
| `Fragment` | Lets you group a list of children without adding extra nodes to the DOM. Can be written as `<>...</>`. | 
| `Profiler` | A component that measures the rendering performance of a React tree to help identify bottlenecks. | 
| `StrictMode` | A tool for highlighting potential problems in an application. It activates additional checks and warnings for its descendants. | 
| `Suspense` | Lets you specify a loading indicator (fallback) while its child components are being loaded asynchronously. | 

## Hooks

Hooks let you use state and other React features in function components. They must be called at the top level of your components.

For a complete guide, see the [Hooks](./hooks.md) documentation.

| API | Description | Related Docs |
|---|---|---|
| `useState` | Adds state to a function component. | [State Hooks](./hooks-state.md) |
| `useEffect` | Performs side effects in function components (e.g., data fetching, subscriptions). | [Effect Hooks](./hooks-effect.md) |
| `useContext` | Accepts a context object and returns the current context value for that context. | [Other Hooks](./hooks-other.md) |
| `useReducer` | An alternative to `useState` for managing more complex state logic. | [State Hooks](./hooks-state.md) |
| `useCallback` | Returns a memoized version of a callback function to prevent unnecessary re-renders. | [Performance Hooks](./hooks-performance.md) |
| `useMemo` | Returns a memoized value, re-computing it only when one of the dependencies has changed. | [Performance Hooks](./hooks-performance.md) |
| `useRef` | Returns a mutable ref object that can hold a value across component renders. | [Ref Hooks](./hooks-ref.md) |
| `useImperativeHandle` | Customizes the instance value that is exposed to parent components when using `ref`. | [Ref Hooks](./hooks-ref.md) |
| `useLayoutEffect` | Similar to `useEffect`, but it fires synchronously after all DOM mutations. | [Effect Hooks](./hooks-effect.md) |
| `useInsertionEffect` | Fires synchronously before DOM mutations are painted, primarily for CSS-in-JS libraries. | [Effect Hooks](./hooks-effect.md) |
| `useDebugValue` | Displays a label for custom hooks in React DevTools. | [Other Hooks](./hooks-other.md) |
| `useDeferredValue` | Defers updating a non-critical part of the UI. | [Performance Hooks](./hooks-performance.md) |
| `useTransition` | Manages state updates without blocking the UI, useful for keeping the UI responsive during slow renders. | [Performance Hooks](./hooks-performance.md) |
| `useId` | Generates unique IDs that are stable on both the server and client, preventing hydration mismatches. | [Other Hooks](./hooks-other.md) |
| `useSyncExternalStore` | A hook for subscribing to an external data source (e.g., a third-party state manager). | [Other Hooks](./hooks-other.md) |
| `useOptimistic` | Manages optimistic UI updates that revert if the underlying asynchronous action fails. | [State Hooks](./hooks-state.md) |
| `useActionState` | Manages the pending and returned value of a form action. | [State Hooks](./hooks-state.md) |
| `use` | A hook for reading the value of a resource, such as a Promise or a context. | [Advanced Guides](./advanced.md) |

## Utilities

These functions help you work with React elements, context, and children.

| API | Description | Related Docs |
|---|---|---|
| `Children` | Provides utilities (`map`, `forEach`, `count`, etc.) for working with the `props.children` data structure. | [Children Utilities](./core-apis-children-utilities.md) |
| `cloneElement` | Clones and returns a new React element using an existing element as a starting point. | [Creating & Manipulating Elements](./core-apis-creating-elements.md) |
| `createContext` | Creates a Context object for passing data through the component tree without prop-drilling. | [Context](./core-apis-context.md) |
| `createElement` | Creates and returns a new React element. JSX compiles to this function call. | [Creating & Manipulating Elements](./core-apis-creating-elements.md) |
| `createRef` | Creates a ref object that can be attached to elements via the `ref` attribute. | [Refs](./core-apis-refs.md) |
| `forwardRef` | Creates a component that can forward a `ref` it receives down to one of its children. | [Refs](./core-apis-refs.md) |
| `isValidElement` | Verifies if an object is a valid React element. | [Creating & Manipulating Elements](./core-apis-creating-elements.md) |
| `lazy` | Defines a component that can be loaded dynamically (code-splitting). Used with `Suspense`. | [Code Splitting](./advanced-code-splitting.md) |
| `memo` | A higher-order component that memoizes a component, preventing re-renders if its props are unchanged. | [Performance Hooks](./hooks-performance.md) |
| `startTransition` | A function to wrap state updates that may cause slow renders, keeping the UI responsive. | [Transitions](./advanced-transitions.md) |

## Caching APIs

These APIs are primarily used for caching data, especially in server environments.

| API | Description | Related Docs |
|---|---|---|
| `cache` | A function to memoize the result of data fetching or computation. | [Caching](./advanced-caching.md) |
| `unstable_getCacheForType` | (Unstable) Retrieves a cache instance for a given resource type. | [Caching](./advanced-caching.md) |
| `unstable_useCacheRefresh` | (Unstable) A hook that returns a function to invalidate the React cache. | [Caching](./advanced-caching.md) |

## Experimental & Unstable APIs

> **Warning**
> These APIs are experimental and may change or be removed in future releases. Use them with caution and only for testing or evaluation purposes.

| API | Description | Related Docs |
|---|---|---|
| `experimental_useEffectEvent` | (Experimental) A hook for defining an event handler that does not re-trigger effects. | [Experimental APIs](./advanced-experimental.md) |
| `unstable_Activity` | (Unstable) A component for managing offscreen UI state. | [Experimental APIs](./advanced-experimental.md) |
| `unstable_postpone` | (Unstable) A function that postpones the rendering of a component tree. | [Experimental APIs](./advanced-experimental.md) |
| `unstable_SuspenseList` | (Unstable) A component that coordinates the loading order of multiple `Suspense` boundaries. | [Experimental APIs](./advanced-experimental.md) |
| `unstable_ViewTransition` | (Unstable) A hook for creating animated view transitions. | [Experimental APIs](./advanced-experimental.md) |

## Other Exports

Miscellaneous exports that provide version information or are intended for specific environments.

| API | Description |
|---|---|
| `version` | A string representing the current version of React (e.g., "18.3.0"). |
| `act` | A testing utility that ensures updates are processed before assertions are made. Available only in development and test builds. |
