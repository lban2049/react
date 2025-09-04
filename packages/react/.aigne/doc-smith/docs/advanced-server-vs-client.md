# Server vs. Client Environments

React can be used in different environments, primarily on the server for initial rendering and on the client (in the browser) for interactivity. The `react` package is designed to support both, providing distinct sets of APIs tailored to the capabilities and requirements of each environment. This distinction is managed through conditional exports in the package's `package.json`, which ensures that you only bundle the code necessary for your target environment.

This guide breaks down the key differences between the server and client environments and their respective APIs.

```d2
direction: down

Server-Environment: {
  label: "Server Environment\n(e.g., Node.js)"
  shape: rectangle

  APIs: {
    shape: package
    label: "Server APIs"
    grid-columns: 2

    createElement: "createElement"
    use: "use"
    cache: "cache"
    useMemo: "useMemo"
  }

  Responsibilities: {
    label: "Responsibilities"
    Data-Fetching: "Data Fetching"
    Initial-Render: "Initial Render"
    Security: "Security (Tainting)"
  }
}

Client-Environment: {
  label: "Client Environment\n(Browser)"
  shape: rectangle

  APIs: {
    shape: package
    label: "Client APIs (Superset)"
    grid-columns: 2

    All-Server-APIs: "All Server APIs"
    useState: "useState"
    useEffect: "useEffect"
    useTransition: "useTransition"
  }
   Responsibilities: {
    label: "Responsibilities"
    Interactivity: "Interactivity"
    State-Management: "State Management"
    DOM-Updates: "DOM Updates"
  }
}

Server-Environment -> Client-Environment: "Sends rendered output to hydrate"
```

## The Server Environment

The server environment entry point (`react/react-server.js`) is designed for rendering components on the server. This is fundamental to patterns like Server-Side Rendering (SSR) and React Server Components (RSC). The API surface is intentionally limited to features that do not depend on a browser's DOM or user interactivity.

Key characteristics of the server environment:
- **No State or Effects:** Hooks that manage state (`useState`, `useReducer`) or side effects (`useEffect`, `useLayoutEffect`) are not available. These hooks are fundamentally tied to the component lifecycle and user interaction in the browser.
- **Data Fetching Focus:** APIs like `cache` are provided to handle server-side data fetching and memoization efficiently across component renders.
- **Security Features:** Includes internal mechanisms like the Taint Registry to prevent sensitive server data from being inadvertently exposed to the client.

APIs available on the server include:
- `Children` utilities (`map`, `forEach`, etc.)
- `Fragment`, `Profiler`, `StrictMode`, `Suspense`
- `createElement`, `cloneElement`, `isValidElement`
- `createRef`, `forwardRef`
- `lazy`, `memo`
- `cache`, `cacheSignal`
- Hooks: `use`, `useId`, `useCallback`, `useDebugValue`, `useMemo`

## The Client Environment

The client environment is the traditional environment for React, running in the user's browser. It includes all the APIs from the server environment plus a comprehensive set of tools for managing state, handling side effects, and directly interacting with the DOM.

Key characteristics of the client environment:
- **Full Interactivity:** Provides access to the complete suite of hooks (`useState`, `useEffect`, `useContext`, etc.) and class component features (`Component`, `PureComponent`) needed to build rich, interactive user interfaces.
- **DOM Management:** Includes APIs like `createContext` and `useRef` for managing and interacting with the DOM tree.
- **Concurrent Features:** Supports concurrent rendering with APIs like `useTransition` and `startTransition` to keep the UI responsive during complex updates.

APIs exclusive to the client environment include:
- `Component`, `PureComponent`
- `createContext`
- `postpone`
- Hooks for state: `useState`, `useReducer`, `useOptimistic`, `useActionState`
- Hooks for effects: `useEffect`, `useLayoutEffect`, `useInsertionEffect`
- Hooks for refs: `useRef`, `useImperativeHandle`
- Hooks for context: `useContext`
- Hooks for concurrency: `useTransition`, `useDeferredValue`
- Other client-specific hooks: `useSyncExternalStore`, `useCacheRefresh`
- Transition management: `startTransition`

## API Availability Comparison

The following table provides a clear comparison of where key React APIs are available.

| API | Server | Client | Notes |
|---|---|---|---|
| `createElement`, `cloneElement` | ✅ | ✅ | Fundamental for creating React elements in any environment. |
| `useState`, `useReducer` | ❌ | ✅ | Manages state, which is a client-side concern tied to interactivity. |
| `useEffect`, `useLayoutEffect` | ❌ | ✅ | Manages side effects (e.g., data fetching, subscriptions) tied to the component lifecycle in the browser. |
| `useContext` | ❌ | ✅ | Reads and subscribes to context. The provider can be rendered on the server, but consumption is for the client. |
| `useMemo`, `useCallback` | ✅ | ✅ | Used for performance optimization via memoization, applicable in both environments. |
| `useRef` | ❌ | ✅ | Provides a mutable reference to a DOM element or a value that persists across renders. |
| `use` | ✅ | ✅ | Can be used to read the value of a Promise or context, designed to work isomorphically. |
| `cache` | ✅ | ✅ | A server-first API for caching data requests, though a client version also exists. |
| `Component`, `PureComponent` | ❌ | ✅ | Class components with their state and lifecycle methods are client-only. |
| `lazy` and `Suspense` | ✅ | ✅ | `Suspense` works on both server and client for async operations. `lazy` is for client-side code splitting. |
| `startTransition`, `useTransition` | ❌ | ✅ | Manages non-urgent UI updates, a core part of the client-side concurrent rendering model. |

## Next Steps

Understanding these environmental differences is crucial for building modern, performant React applications. To dive deeper into related topics, explore how React handles data fetching and memoization.

<x-card data-title="Caching" data-icon="lucide:database-zap" data-href="/advanced/caching" data-cta="Learn about Caching">
Learn how to use React's caching capabilities for data fetching on both client and server.
</x-card>
