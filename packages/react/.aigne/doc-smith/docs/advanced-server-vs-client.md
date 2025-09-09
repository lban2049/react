# Server vs. Client Environments

React is designed to build user interfaces that can be rendered on both the server and the client (typically a web browser). This flexibility is a cornerstone of modern React architecture, enabling patterns like Server-Side Rendering (SSR) and React Server Components. Understanding the distinction between these environments is key to leveraging React's full potential, as each environment has access to a different set of APIs tailored to its purpose.

## Dual Entry Points

The `react` package formalizes this separation by providing different entry points for build tools. This is explicitly defined in its `package.json` file under the `exports` map. When a build tool encounters an import from `react` in a server environment (like for a React Server Component), it resolves to a server-specific file.

```json package.json icon=logos:npm
{
  "exports": {
    ".": {
      "react-server": "./react.react-server.js",
      "default": "./index.js"
    },
    "./jsx-runtime": {
      "react-server": "./jsx-runtime.react-server.js",
      "default": "./jsx-runtime.js"
    },
    "./jsx-dev-runtime": {
      "react-server": "./jsx-dev-runtime.react-server.js",
      "default": "./jsx-dev-runtime.js"
    }
    // ... other exports
  }
}
```

This configuration ensures that only the necessary code is bundled for each environment, optimizing the application's performance.

```d2
direction: down

react-package: {
  label: "'react' NPM Package"
  shape: rectangle

  client-entry: {
    label: "index.js\n(Default Entry)"
  }

  server-entry: {
    label: "react.react-server.js\n('react-server' Entry)"
  }
}

client-env: {
  label: "Client Environment\n(Browser)"
  shape: rectangle
  APIs: {
    label: "APIs for Interactivity"
    "- useState()\n- useEffect()\n- useTransition()\n- Component Class"
  }
}

server-env: {
  label: "Server Environment\n(e.g., Node.js)"
  shape: rectangle
  APIs: {
    label: "APIs for Rendering & Data"
    "- cache()\n- use() for Promises\n- No state or effects"
  }
}

react-package.client-entry -> client-env: "Resolves to"
react-package.server-entry -> server-env: "Resolves to"
```

## Core API Differences

The most significant difference lies in the set of APIs available. The server environment is focused on non-interactive rendering and data fetching, while the client environment adds APIs for state management, side effects, and user interaction.

Below is a comparison of some key APIs and their availability:

| API Name | Available on Server | Available on Client | Notes |
| :--- | :---: | :---: | :--- |
| `createElement` | ✅ | ✅ | The fundamental API for creating React elements. |
| `Fragment`, `Profiler`, `StrictMode`, `Suspense` | ✅ | ✅ | Core components for structuring and debugging applications. |
| `useState`, `useReducer`, `useActionState` | ❌ | ✅ | Hooks for managing component state. Not available on the server. |
| `useEffect`, `useLayoutEffect`, `useInsertionEffect` | ❌ | ✅ | Hooks for performing side effects. Client-only. |
| `useRef`, `useImperativeHandle` | ❌ | ✅ | Hooks for referencing DOM nodes or component instances. |
| `useContext` | ❌ | ✅ | Hook for consuming context. Essential for client-side state propagation. |
| `Component`, `PureComponent` | ❌ | ✅ | Class components are a client-only feature. |
| `useTransition`, `startTransition` | ❌ | ✅ | APIs for managing non-blocking UI updates. |
| `cache` | ✅ | ✅ | Used for memoizing data fetches. While available on both, it's primarily designed for server-side data fetching within Server Components. |
| `use` | ✅ | ✅ | A versatile Hook that can read the value of a promise (server and client) or context (client-only). |

## A Tale of Two Internals

This separation extends deep into React's internal workings. Each environment uses a distinct shared state object (`ReactSharedInternalsServer` vs. `ReactSharedInternalsClient`), tailored to its specific needs.

For example, the server's internal state includes logic for security features like Taint Prevention, which is irrelevant on the client. Conversely, the client's internal state manages transition states and queues for the `act` testing utility.

This internal divergence highlights that the two environments are not just subsets of one another but are truly distinct runtimes optimized for different tasks.

## Conclusion

In summary, choosing the right API depends entirely on where your component will run. Server Components can leverage server-only capabilities like direct database access and the `cache` function but cannot use state or effects. Client Components, often marked with a `"use client"` directive, execute in the browser and have access to the full suite of Hooks for building rich, interactive experiences.

To see how these environments work together to optimize your application's loading performance, dive into our guide on [Code Splitting with `lazy` and `Suspense`](./advanced-code-splitting.md).