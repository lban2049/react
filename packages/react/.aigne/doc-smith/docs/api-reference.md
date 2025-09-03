# API Reference

This section provides a comprehensive reference for all public APIs in React. It's designed to help you quickly find details about any React feature you need to use. The APIs are grouped by category for easier navigation.

For a more guided introduction, see the [Core APIs](./core-apis.md) and [Hooks](./hooks.md) sections.

<x-cards data-columns="2">
  <x-card data-title="Core Components & APIs" data-icon="lucide:box" data-href="#core-components-apis">
    Fundamental building blocks like Components, Fragments, and Suspense.
  </x-card>
  <x-card data-title="Hooks" data-icon="lucide:anchor" data-href="#hooks">
    Functions that let you hook into React state and lifecycle features.
  </x-card>
  <x-card data-title="Utilities" data-icon="lucide:wrench" data-href="#utilities">
    Helper functions for working with elements, children, and more.
  </x-card>
  <x-card data-title="Unstable & Experimental APIs" data-icon="lucide:flask-conical" data-href="#unstable--experimental-apis">
    APIs that are in development and subject to change.
  </x-card>
</x-cards>


## Core Components & APIs

These are the primary exports you'll use to define and structure your application's UI.

| API | Description |
|---|---|
| `Component` | The base class for React components when using ES6 classes. |
| `PureComponent` | Similar to `Component` but implements `shouldComponentUpdate()` with a shallow prop and state comparison. |
| `Fragment` | Lets you group a list of children without adding extra nodes to the DOM. |
| `Profiler` | Measures rendering performance of a React component tree. |
| `StrictMode` | A tool for highlighting potential problems in an application. It activates additional checks and warnings for its descendants. |
| `Suspense` | Lets you display a fallback UI while your components are loading data. See the guide on [Code Splitting](./advanced-code-splitting.md). |
| `lazy` | Lets you define a component that is loaded dynamically. |
| `memo` | A higher-order component for memoizing a component, preventing re-renders if its props haven't changed. |

## Hooks

Hooks allow you to use state and other React features in function components. They can only be called at the top level of your function components or from your own custom Hooks. For detailed usage, see the main [Hooks documentation](./hooks.md).

| Hook | Description |
|---|---|
| [`useState`](./hooks-state.md) | Adds state to a function component. |
| [`useEffect`](./hooks-effect.md) | Lets you perform side effects in function components. |
| [`useContext`](./hooks-other.md) | Accepts a context object and returns the current context value. |
| [`useReducer`](./hooks-state.md) | An alternative to `useState` for managing complex state logic. |
| [`useCallback`](./hooks-performance.md) | Returns a memoized callback function. |
| [`useMemo`](./hooks-performance.md) | Returns a memoized value. |
| [`useRef`](./hooks-ref.md) | Returns a mutable ref object. |
| [`useImperativeHandle`](./hooks-ref.md) | Customizes the instance value that is exposed to parent components when using `ref`. |
| [`useLayoutEffect`](./hooks-effect.md) | Fires synchronously after all DOM mutations. |
| [`useInsertionEffect`](./hooks-effect.md) | Fires synchronously before all DOM mutations, typically for CSS-in-JS libraries. |
| [`useDebugValue`](./hooks-other.md) | Can be used to display a label for custom hooks in React DevTools. |
| [`useTransition`](./hooks-performance.md) | Lets you update the state without blocking the UI. |
| [`useDeferredValue`](./hooks-performance.md) | Lets you defer updating a part of the UI. |
| [`useId`](./hooks-other.md) | Generates unique IDs that are stable across the server and client. |
| [`useSyncExternalStore`](./hooks-other.md) | A hook for subscribing to an external store. |
| [`useActionState`](./hooks-state.md) | A hook to manage the state of a form action. |
| [`useOptimistic`](./hooks-state.md) | A hook that lets you optimistically update the UI. |
| `use` | A hook for reading the value of a resource, like a Promise or context. |

## Utilities

React provides a set of utility functions to help you work with elements, children, and other core concepts.

### Element Manipulation

| Utility | Description |
|---|---|
| `createElement` | Creates and returns a new React element of the given type. |
| `cloneElement` | Clones and returns a new React element using an element as the starting point. |
| `isValidElement` | Verifies the object is a React element. |

### Refs & Context

| Utility | Description |
|---|---|
| `createRef` | Creates a ref object that can be attached to React elements. |
| `forwardRef` | Creates a React component that forwards the ref it receives to another component below in the tree. |
| `createContext` | Creates a Context object. See the guide on [Context](./core-apis-context.md). |

### Children Utilities

The `React.Children` object provides utilities for dealing with the `props.children` data structure.

| Utility | Description |
|---|---|
| `Children.map` | Invokes a function on every immediate child contained within `children`. |
| `Children.forEach` | Like `Children.map()` but does not return an array. |
| `Children.count` | Returns the total number of components in `children`. |
| `Children.toArray` | Returns the `children` opaque data structure as a flat array. |
| `Children.only` | Verifies that `children` has only one child and returns it. |

### Other Utilities

| Utility | Description |
|---|---|
| `version` | A string containing the current React version. |
| `act` | A helper for testing components that wraps renders and updates in a way that is closer to how React works in the browser. (DEV-only) |
| `cache` | A utility for memoizing data-fetching functions. See [Caching](./advanced-caching.md). |

## Unstable & Experimental APIs

> **Warning**
> These APIs are not yet stable and may be changed or removed in a future release. Use them at your own risk in production applications.

| API | Description |
|---|---|
| `unstable_SuspenseList` | Coordinates the loading order of multiple Suspense components. |
| `unstable_LegacyHidden` | A component for hiding a subtree from rendering. |
| `unstable_Activity` | A component used to indicate that a subtree is currently active or visible. |
| `unstable_Scope` | Provides a way to scope event listeners to a particular subtree. |
| `unstable_TracingMarker` | An internal component used for performance tracing. |
| `unstable_ViewTransition` | A component to enable view transitions for navigations. |
| `unstable_useCacheRefresh` | A hook to invalidate the React cache. |
| `unstable_getCacheForType` | Retrieves a cache instance for a given type. |
| `experimental_useEffectEvent` | A hook for declaring an Effect Event, which is a non-reactive piece of logic inside an Effect. |