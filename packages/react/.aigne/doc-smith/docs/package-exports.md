# Package Exports

The `react` npm package is designed for versatility across different environments and build setups. Beyond the main `import React from 'react'` that most developers use, the package exposes several other entry points. These are formally defined in the `package.json` `exports` field, which allows build tools and JavaScript runtimes to select the correct file for a given environment.

Understanding these exports is useful for configuring build tools, optimizing bundles, and leveraging React features in specific contexts like server environments.

## Main Entry Points

The `react` package provides distinct entry points for its core functionality, the JSX runtime, and the compiler runtime. Many of these entry points also have different versions for client and server environments, which is crucial for features like React Server Components.

Here is a breakdown of the primary exports available:

| Export Path | Description |
|---|---|
| `react` | The main entry point for the React library. It provides the core APIs like `useState`, `useEffect`, etc. It conditionally loads different modules for server and client environments. |
| `react/jsx-runtime` | The JSX runtime. Modern build tools use this to transform JSX into function calls automatically, without needing `React` to be in scope. This is the default for production builds. |
| `react/jsx-dev-runtime` | A developer-focused version of the JSX runtime. It includes extra validation and warnings to help catch potential issues during development. |
| `react/compiler-runtime` | Provides runtime helper functions required by the React Compiler. This module is typically used automatically when the compiler is enabled in a project, rather than being imported directly by the developer. |

## Development vs. Production Builds

Each entry point dynamically selects a file based on the `process.env.NODE_ENV` environment variable. When `NODE_ENV` is set to `'production'`, the optimized production build is used. Otherwise, the development build, which includes helpful warnings and debugging tools, is loaded.

This mechanism is consistent across all entry points, as seen in their respective files:

```javascript
// Content of react/index.js
'use strict';

if (process.env.NODE_ENV === 'production') {
  module.exports = require('./cjs/react.production.js');
} else {
  module.exports = require('./cjs/react.development.js');
}
```

The following diagram illustrates this conditional loading flow:

```d2
direction: down

"import 'react'": {
  shape: step
  label: "Application or library imports an entry point, e.g., 'react'"
}

"check_env": {
  shape: diamond
  label: "process.env.NODE_ENV === 'production'?"
}

"prod_module": {
  shape: package
  label: "Loads optimized production module\n(e.g., react.production.js)"
  style.fill: "#E5F9E0"
}

"dev_module": {
  shape: package
  label: "Loads development module with warnings\n(e.g., react.development.js)"
  style.fill: "#FEF3C7"
}

"import 'react'" -> "check_env"
"check_env" -> "prod_module": "Yes"
"check_env" -> "dev_module": "No"
```

## Server vs. Client Environments

The `exports` map in `package.json` also defines a `"react-server"` condition. This allows bundlers configured for a server environment (e.g., for rendering React Server Components) to resolve to a different set of files, such as `react.react-server.js` or `jsx-runtime.react-server.js`.

This separation ensures that server-only and client-only code is not bundled for the wrong environment, which is fundamental to how modern React frameworks operate.

For a deeper dive into this topic, see the [Server vs. Client Environments](./advanced-server-vs-client.md) guide.

## How These Exports Are Used

In most modern React projects, you won't import these sub-paths directly. Instead, your build tools are configured to handle them for you.

For example, the "automatic" JSX transform in Babel or TypeScript is configured to import `react/jsx-runtime` or `react/jsx-dev-runtime` behind the scenes. This is what allows you to write JSX in a file without `import React from 'react'`.

```json
// Example .babelrc configuration (conceptual)
{
  "presets": [
    [
      "@babel/preset-react",
      {
        "runtime": "automatic" // This tells Babel to use react/jsx-runtime
      }
    ]
  ]
}
```

By understanding these package exports, you gain insight into how the React ecosystem is structured to support various platforms and development needs, from simple client-side applications to complex server-rendered experiences.