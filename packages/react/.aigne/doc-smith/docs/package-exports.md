# Package Exports

The `react` npm package provides several entry points to support different environments and toolchains. This structure allows build tools to pick the correct version of React based on the context, such as development versus production, or client versus server rendering. This guide details these exports, explaining what each one is for and how they are used.

## Summary of Exports

The main entry points are defined in the `exports` field of React's `package.json`. Here is a summary of the most important ones:

| Export Path              | Description                                                                                              | Notes                                                                                             |
| ------------------------ | -------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------- |
| `react`                  | The main entry point for the core React library. Used to import APIs like `useState` and `useEffect`.    | Conditionally loads development or production builds. Also has a `react-server` variant.          |
| `react/jsx-runtime`      | The production JSX runtime for the automatic JSX transform.                                              | Used by compilers (like Babel or SWC). You typically don't import this directly in your code.    |
| `react/jsx-dev-runtime`  | The development JSX runtime, which includes extra validation and helpful warnings.                       | Used by compilers in development mode.                                                            |
| `react/compiler-runtime` | The runtime helper library for the React Compiler.                                                       | This is an internal package used by the compiler and is not intended for direct use.              |
| `react/package.json`     | Exposes the package's `package.json` file itself.                                                        | Useful for tooling that needs to inspect React's metadata, like its version.                      |

## How Exports are Resolved

When you write an import statement like `import { useState } from 'react'`, your build tool (like Webpack or Vite) or the Node.js runtime resolves it using the `exports` map. The process generally follows these steps:

```d2
direction: down

"Your Code": {
  shape: document
  label: "import { useState } from 'react';"
}

"Build Tool": {
  shape: rectangle
  label: "Bundler / Node.js"
}

"package.json": {
  shape: document
  label: "react/package.json"
}

"Entry Point Script": {
  shape: document
  label: "e.g., index.js"
}

"Final Module": {
  shape: package
  grid-columns: 2
  "react.development.js": {}
  "react.production.js": {}
}

"Your Code" -> "Build Tool": "1. Process import"
"Build Tool" -> "package.json": "2. Read 'exports' map"
"package.json" -> "Entry Point Script": "3. Select entry based on conditions (e.g., 'default')"
"Entry Point Script" -> "Final Module": "4. Select build based on NODE_ENV"

```

This system ensures that you get the most appropriate version of React for your specific situation without needing to configure it manually.

## Detailed Entry Points

### Core Entry Point (`react`)

This is the standard entry point you use every day. It provides all the core React APIs.

```javascript
import React, { useState, useEffect } from 'react';
```

The file that gets loaded (`index.js`) contains logic to switch between the development and production builds based on the `process.env.NODE_ENV` variable.

```javascript
// A simplified view of react/index.js
'use strict';

if (process.env.NODE_ENV === 'production') {
  module.exports = require('./cjs/react.production.js');
} else {
  module.exports = require('./cjs/react.development.js');
}
```

- **Development builds** (`react.development.js`) are larger and include helpful warnings and error messages to aid in debugging.
- **Production builds** (`react.production.js`) are smaller, optimized, and stripped of development-only warnings.

### JSX Runtimes (`react/jsx-runtime` and `react/jsx-dev-runtime`)

These entry points support the modern JSX transform. With this transform, you no longer need to import `React` into every file that uses JSX. Your compiler automatically imports the necessary functions from these runtimes.

- `react/jsx-runtime` is used for production builds.
- `react/jsx-dev-runtime` is used during development and adds source file information to warnings.

You will likely never need to import these manually; your build tooling handles it.

### Compiler Runtime (`react/compiler-runtime`)

This entry point contains helper functions required by the React Compiler (formerly known as React Forget). The compiler automatically optimizes your React components, and this runtime provides the necessary support code for those optimizations. It is not considered part of the public API and should not be imported directly.

### Server and Client Conditions

The `exports` map also defines special conditions, most notably `react-server`. This condition is used by frameworks and bundlers to resolve a different set of files when building for a React Server Components environment. This allows React to provide specialized builds for the server that are distinct from the client-side builds.

For more information on this topic, please see the [Server vs. Client Environments](./advanced-server-vs-client.md) guide.