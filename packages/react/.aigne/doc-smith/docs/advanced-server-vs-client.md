# Server vs. Client Environments

React is designed to run in different JavaScript environments, primarily the client (in a web browser) and the server (with Node.js or similar runtimes). To support this, the `react` package provides distinct sets of APIs tailored to each environment. This separation allows for optimized builds and prevents the use of environment-specific APIs in the wrong context.

This distinction is formally managed through conditional exports in the `react` package's `package.json` file.

## Conditional Exports

The `react` package uses a modern module feature called conditional exports to expose different files depending on the environment. When a build tool or runtime, such as Webpack or Next.js, detects a server environment, it can use the `"react-server"` condition to import a server-specific version of React and its APIs.

Here is a simplified view of how this is defined in `react/package.json`:

```json
{
  "exports": {
    ".": {
      "react-server": "./react.react-server.js",
      "default": "./index.js"
    },
    "./jsx-runtime": {
      "react-server": "./jsx-runtime.react-server.js",
      "default": "./jsx-runtime.js"
    }
  }
}
```

This mechanism is key to how React separates concerns. The `default` export points to the client-side build, which includes everything needed for interactivity in a browser. The `react-server` export points to a build that is streamlined for server-side rendering, excluding APIs that are irrelevant or would cause errors on the server.

```d2
direction: down

"Bundler / Runtime": {
  shape: rectangle
}

"react package": {
  shape: package
  grid-columns: 2
  grid-gap: 80

  "Client Entry (`index.js`)": {
    label: "Client Environment\n(default export)"
    shape: document
    "APIs": {
      "useState": "Stateful logic"
      "useEffect": "Side effects"
      "Component": "Class components"
      "startTransition": "Concurrent UI"
    }
  }

  "Server Entry (`react.react-server.js`)": {
    label: "Server Environment\n('react-server' export)"
    shape: document
    "APIs": {
      "cache": "Server-side data caching"
      "use": "Read promises/context"
      "Fragment": "Group elements"
      "createElement": "Core element factory"
    }
  }
}

"Bundler / Runtime" -> "react package": "Resolves module based on environment"
```

## API Availability Comparison

The most significant difference between the environments is the set of available APIs. Client environments include APIs for interactivity, stateful logic, and browser-specific side effects. The server environment provides a more limited set of APIs focused on rendering and data management.

Below is a comparison of key APIs and their availability.

| API | Client Environment | Server Environment | Notes |
|---|---|---|---|
| **State Hooks** | | | |
| `useState`, `useReducer` | ✅ Available | ❌ Not Available | Manages component state, which is a client-side concept tied to user interaction and re-renders. |
| `useActionState`, `useOptimistic` | ✅ Available | ❌ Not Available | Hooks designed for managing form state and pending UI updates on the client. |
| **Effect Hooks** | | | |
| `useEffect`, `useLayoutEffect`, `useInsertionEffect` | ✅ Available | ❌ Not Available | Used to perform side effects that interact with the browser DOM and lifecycle, which do not exist on the server. |
| **Component Types** | | | |
| `Component`, `PureComponent` | ✅ Available | ❌ Not Available | Class components and their lifecycle methods are part of the client-side component model. |
| **Concurrency APIs** | | | |
| `startTransition`, `useTransition`, `useDeferredValue` | ✅ Available | ❌ Not Available | APIs for managing concurrent rendering transitions, which are primarily for keeping the UI responsive during updates on the client. |
| **Shared Hooks & APIs** | | | |
| `use`, `useId`, `useMemo`, `useCallback`, `useDebugValue` | ✅ Available | ✅ Available | Hooks that are not dependent on a specific client or server lifecycle. |
| `cache`, `cacheSignal` | ✅ Available | ✅ Available | Provides a mechanism for memoization and caching. The underlying implementation is optimized for each specific environment. |
| `createElement`, `cloneElement`, `isValidElement` | ✅ Available | ✅ Available | Fundamental APIs for creating and manipulating React elements are available everywhere. |
| `Fragment`, `Profiler`, `StrictMode`, `Suspense` | ✅ Available | ✅ Available | Core components for structuring and debugging applications. |

## Shared Internals

Internally, React maintains separate shared state objects for each environment: `ReactSharedInternalsClient` and `ReactSharedInternalsServer`. These objects hold the current state needed by hooks and other features, such as the active dispatcher for hooks or the current transition state.

For example, `ReactSharedInternalsClient` includes properties like `actQueue` for testing and `isBatchingLegacy` for legacy mode, which are irrelevant on the server. Conversely, `ReactSharedInternalsServer` is configured to handle features like the Taint APIs for security when enabled.

While you will not interact with these internal objects directly, their existence is fundamental to how React isolates environment-specific logic and maintains a clean separation of concerns.

## Practical Implications

Understanding this separation is crucial when developing applications that utilize server-side rendering or React Server Components. When writing a component intended to run on both the server and the client, you must only use the shared API surface.

- **Avoid Client-Only APIs on the Server**: Attempting to use a hook like `useState` or `useEffect` in a component that renders exclusively on the server will result in an error.
- **Optimized Server Builds**: This separation allows for smaller, more efficient server builds that exclude the code for client-side interactivity, state management, and effects.

By respecting these environment boundaries, you can build applications that are both robust and performant, taking full advantage of React's capabilities on both the server and the client.

---

### Next Steps

Explore these related topics to further understand how React leverages different environments.

<x-cards data-columns="2">
  <x-card data-title="Caching" data-icon="lucide:database" data-href="/advanced/caching">
    Learn how React's caching capabilities work on both the client and the server for data fetching and memoization.
  </x-card>
  <x-card data-title="Code Splitting with lazy and Suspense" data-icon="lucide:splitsquare-horizontal" data-href="/advanced/code-splitting">
    Discover how to improve app performance by loading components only when they are needed, a pattern that works with Suspense in both environments.
  </x-card>
</x-cards>