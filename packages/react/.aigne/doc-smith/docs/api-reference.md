# API Reference

The `react` package provides the core APIs necessary to define and manage components in a React application. This section serves as a comprehensive reference for all public APIs, including Hooks, built-in components, classes, and top-level utilities. 

Whether you're building with modern functional components or traditional class-based components, you can find the detailed information you need in the following sections.

<x-cards data-columns="2">
  <x-card data-title="Hooks" data-icon="lucide:anchor" data-href="/api-reference/hooks">
    Detailed documentation for all built-in React Hooks, such as useState, useEffect, and useContext, with usage examples for each.
  </x-card>
  <x-card data-title="Components & Classes" data-icon="lucide:box" data-href="/api-reference/components">
    Reference for built-in components and classes like Component, PureComponent, Fragment, and Suspense, which form the foundation of React applications.
  </x-card>
  <x-card data-title="Top-Level APIs" data-icon="lucide:function-square" data-href="/api-reference/top-level">
    Documentation for top-level React functions such as createElement, createContext, forwardRef, lazy, and memo, used for various advanced patterns and optimizations.
  </x-card>
  <x-card data-title="React.Children" data-icon="lucide:cuboid" data-href="/api-reference/children">
    A guide to using the React.Children utility for working with the props.children data structure, allowing for powerful manipulation and inspection of child elements.
  </x-card>
</x-cards>

## Complete Export List

For a quick overview, the following table lists all major public APIs exported from the `react` package.

| API                  | Category                | Description                                                                                             |
| -------------------- | ----------------------- | ------------------------------------------------------------------------------------------------------- |
| `Component`            | Components & Classes    | The base class for React components when using ES6 classes.                                             |
| `PureComponent`        | Components & Classes    | Similar to `Component`, but with a built-in shallow prop and state comparison to prevent re-renders.    |
| `Fragment`             | Components & Classes    | Lets you group a list of children without adding an extra node to the DOM.                                |
| `Profiler`             | Components & Classes    | Measures rendering performance of a React tree for optimization purposes.                               |
| `StrictMode`           | Components & Classes    | A tool for highlighting potential problems in an application during development.                        |
| `Suspense`             | Components & Classes    | Lets you specify a loading indicator for parts of your component tree that are not yet ready to render. |
| `use`                  | Hook                    | A Hook for reading the value of a resource, such as a Promise or context.                               |
| `useCallback`          | Hook                    | Returns a memoized callback function.                                                                   |
| `useContext`           | Hook                    | Accepts a context object and returns its current value.                                                 |
| `useDebugValue`        | Hook                    | Displays a label for custom hooks in React DevTools.                                                    |
| `useDeferredValue`     | Hook                    | Defers updating a part of the UI.                                                                       |
| `useEffect`            | Hook                    | Lets you perform side effects in function components.                                                   |
| `useId`                | Hook                    | A Hook for generating unique IDs that are stable across server and client rendering.                    |
| `useImperativeHandle`  | Hook                    | Customizes the instance value that is exposed to parent components when using `ref`.                    |
| `useInsertionEffect`   | Hook                    | Similar to `useEffect`, but it fires synchronously before all DOM mutations.                            |
| `useLayoutEffect`      | Hook                    | Fires synchronously after all DOM mutations.                                                            |
| `useMemo`              | Hook                    | Returns a memoized value.                                                                               |
| `useOptimistic`        | Hook                    | A Hook that lets you optimistically update the UI.                                                      |
| `useReducer`           | Hook                    | An alternative to `useState` for managing complex state logic.                                          |
| `useRef`               | Hook                    | Returns a mutable ref object.                                                                           |
| `useState`             | Hook                    | A Hook that lets you add state to function components.                                                  |
| `useSyncExternalStore` | Hook                    | A Hook that lets you subscribe to an external store.                                                    |
| `useTransition`        | Hook                    | A Hook to mark state updates as non-urgent, improving user experience for slow renders.               |
| `useActionState`       | Hook                    | A Hook to manage the state of a form action.                                                            |
| `cloneElement`         | Top-Level API           | Clones and returns a new React element using an element as the starting point.                          |
| `createContext`        | Top-Level API           | Creates a Context object for passing data through the component tree.                                   |
| `createElement`        | Top-Level API           | Creates and returns a new React element. Usually used via JSX.                                          |
| `createRef`            | Top-Level API           | Creates a ref that can be attached to React elements via the ref attribute.                             |
| `forwardRef`           | Top-Level API           | Creates a component that forwards a ref to a child component.                                           |
| `isValidElement`       | Top-Level API           | Verifies if an object is a React element.                                                               |
| `lazy`                 | Top-Level API           | Lets you define a component that is loaded dynamically (code-splitting).                                |
| `memo`                 | Top-Level API           | A higher-order component that memoizes a component, preventing re-renders if its props are unchanged. |
| `startTransition`      | Top-Level API           | Lets you mark state updates as non-urgent.                                                              |
| `Children`             | React.Children          | An object with utilities for working with `props.children`.                                             |
| `version`              | Package Information     | A string containing the current version of React.                                                       |

---

This reference provides a high-level overview of the APIs available in the `react` package. For in-depth explanations and code examples, please explore the detailed guides for each category.

To get started with the most common APIs for building modern applications, head to the [Hooks](./api-reference-hooks.md) documentation next.