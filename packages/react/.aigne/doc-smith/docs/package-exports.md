# Package Exports

The `react` package is designed for modern JavaScript environments and bundlers. It leverages Node.js Package Exports to provide multiple entry points, allowing tools to select the appropriate version of the library for different use cases and environments, such as development versus production, or client versus server.

Understanding these exports is useful for configuring build tools or for advanced use cases where you need to interact with specific parts of the React ecosystem, like the JSX runtime.

## Main Entry Points

The `react` package defines several entry points in its `package.json`. Here is a summary of the primary exports you will encounter:

| Export Path | Description |
|---|---|
| `react` | The main entry point for the core React library. This is what you get when you write `import React from 'react'`. It provides access to APIs like `useState`, `useEffect`, `createElement`, etc. |
| `react/jsx-runtime` | The production JSX runtime. This entry point is used by compilers (like Babel or TypeScript) for the automatic JSX transform, which converts JSX syntax into `jsx()` function calls. You typically don't import this directly. |
| `react/jsx-dev-runtime` | The development version of the JSX runtime. It includes additional warnings and debugging information, such as source file and line number tracking, which is helpful during development. |
| `react/compiler-runtime` | Contains runtime helper functions required by the React Compiler. This code is automatically injected by the compiler where needed and is not intended for direct use by developers. |
| `react/package.json` | Exposes the package's `package.json` file, which can be useful for tools that need to inspect package metadata. |

---

## Conditional Exports

React uses conditional exports to serve different files based on the environment. This ensures that you get the best performance in production while having helpful warnings and tools during development.

### Development vs. Production Builds

Most entry points, including the main `react` export and the JSX runtimes, check the `process.env.NODE_ENV` variable. Based on its value, they load either a development or a production build.

- **Development (`NODE_ENV !== 'production'`)**: The development builds are larger and include extensive warnings, error messages, and debugging aids.
- **Production (`NODE_ENV === 'production'`)**: The production builds are minified and optimized for performance, with all development-only code stripped out.

Here is the logic from the main `index.js` entry point, which illustrates this pattern:

```javascript index.js icon=logos:javascript
'use strict';

if (process.env.NODE_ENV === 'production') {
  module.exports = require('./cjs/react.production.js');
} else {
  module.exports = require('./cjs/react.development.js');
}
```

Your build tool (like Webpack, Vite, or Parcel) is responsible for setting `process.env.NODE_ENV` correctly, which automatically enables these optimizations for your production application.

### React Server vs. Client Environments

The `exports` map also includes a `"react-server"` condition. This allows bundlers and frameworks that support React Server Components to resolve to a specific build of React that is designed to run in a server-only environment.

For example, the main `.` export is defined as:

```json package.json icon=logos:npm
"exports": {
  ".": {
    "react-server": "./react.react-server.js",
    "default": "./index.js"
  },
  // ...other exports
}
```

In an environment that understands the `"react-server"` condition, importing `react` will resolve to `react.react-server.js`. In all other environments (like a traditional client-side application), it will resolve to the `default` export, `index.js`.

This same conditional logic applies to the JSX runtimes, ensuring that the correct version is used whether your components are rendering on the server or the client. For a deeper dive into this topic, see our guide on [Server vs. Client Environments](./advanced-server-vs-client.md).
