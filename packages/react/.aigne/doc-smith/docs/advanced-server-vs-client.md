# Server vs. Client Environments

React is designed to build user interfaces in various environments. The primary distinction is between the **server** and the **client** (the browser). This separation enables powerful rendering strategies like Server-Side Rendering (SSR) and the use of React Server Components. While a large portion of the React API is universal, certain functions and hooks are exclusive to one environment. This guide explores these differences and how React manages them.

## Conditional Package Exports

The distinction between environments is managed at the package level through conditional exports. When you import from the `react` package, your build tool or runtime resolves to a different file depending on whether it's building for a server or client environment. This logic is defined in React's `package.json` file:

```json
// A simplified view of react/package.json
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

The `react-server` condition points to server-specific builds, while `default` typically resolves to the client-side build for browsers.

This mechanism allows React to provide an optimized set of APIs for each context.

```d2
direction: down

Bundler: "Build Tool or Runtime"

package_json: {
  shape: document
  label: "react/package.json"
}

condition_check: {
  shape: diamond
  label: "Is this a 'react-server' environment?"
}

server_entry: {
  label: "Loads './react.react-server.js' (Server APIs)"
  style: {
    fill: "#d3e5ff"
  }
}

client_entry: {
  label: "Loads './index.js' (Client APIs)"
  style: {
    fill: "#d0f0c0"
  }
}

Bundler -> package_json: "Reads 'exports' field"
package_json -> condition_check: "Evaluates conditions"
condition_check -> server_entry: "Yes"
condition_check -> client_entry: "No (uses 'default')"
```

## API Availability

While many core concepts like Components, Elements, and some Hooks are universal, a significant number of APIs are environment-specific. The client environment has a richer set of APIs for interactivity, state management, and side effects that directly manipulate the DOM.

| API | Environment(s) | Description |
|---|---|---|
| **Universal APIs** | | |
| `createElement`, `cloneElement`, `isValidElement` | Server & Client | Core functions for creating and working with React elements. |
| `Fragment`, `Profiler`, `StrictMode`, `Suspense` | Server & Client | Built-in components for structuring UI and managing rendering behavior. |
| `useMemo`, `useCallback`, `useDebugValue` | Server & Client | Hooks for optimization and debugging, available in both runtimes. |
| `useId` | Server & Client | Generates stable, unique IDs that work safely with server rendering. |
| `use` | Server & Client | Reads the value of a resource like a Promise or context. |
| `lazy`, `memo`, `forwardRef` | Server & Client | Utilities for optimizing components and forwarding refs. |
| **Client-Only APIs** | | |
| `useState`, `useReducer`, `useRef`, `useActionState`, `useOptimistic` | Client | Hooks to manage state, complex state logic, and references to DOM elements or values. |
| `useEffect`, `useLayoutEffect`, `useInsertionEffect`| Client | Hooks for performing side effects that interact with the browser environment (e.g., data fetching, subscriptions, DOM mutations). |
| `useContext` | Client | Hook for reading and subscribing to context. |
| `startTransition`, `useTransition` | Client | APIs for marking UI updates as non-urgent to avoid blocking user input. |
| `Component`, `PureComponent` | Client | Base classes for creating stateful class components. |
| `createContext` | Client | Creates a Context object for sharing data without prop drilling. |
| `act` | Client | A testing utility to ensure updates are processed before assertions are made. |
| **Server-Only APIs** | | |
| `cache`, `cacheSignal` | Server | A function to memoize data fetches or computations across component renderings within a single server request. `cacheSignal` is an associated utility. |
| `__SERVER_INTERNALS_DO_NOT_USE_OR_WARN_USERS_THEY_CANNOT_UPGRADE` | Server | An internal object exposing server-specific state, not for direct use. |

## Internal State Differences

Beyond the public API, the internal state managed by React also differs. Each environment has a unique `ReactSharedInternals` object tailored to its specific needs.

- **Server Internals (`ReactSharedInternalsServer`)**: Focuses on request-level concerns. For example, when `enableTaint` is active, it includes registries for experimental security features (`TaintRegistryObjects`, `TaintRegistryValues`) which help prevent data leaks from the server.

- **Client Internals (`ReactSharedInternalsClient`)**: Manages state related to browser interactivity and rendering. This includes queues for the `act` testing utility (`actQueue`), state for concurrent transitions (`asyncTransitions`), and flags to track asynchronous work within a render batch (`didUsePromise`).

## Summary

Understanding the distinction between React's server and client environments is key for building modern, performant applications. This dual-runtime architecture allows you to leverage the best of both worlds: fast initial page loads from the server and rich interactivity on the client. By being aware of which APIs are available in each context, you can write more efficient and robust code.

### Next Steps

To learn more about features that leverage this architecture, explore these guides:

<x-cards>
  <x-card data-title="Caching" data-icon="lucide:database-zap" data-href="/advanced/caching">
    Dive deeper into React's caching mechanisms, which are available on both the server and client but have distinct use cases.
  </x-card>
  <x-card data-title="Code Splitting with lazy and Suspense" data-icon="lucide:split" data-href="/advanced/code-splitting">
    Learn how to use `Suspense` to handle asynchronous operations like data fetching on the server and code splitting on the client.
  </x-card>
</x-cards>