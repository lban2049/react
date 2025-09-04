# Package Exports

The `react` npm package exposes several distinct entry points to support different environments and build configurations. These exports, defined in the package's `package.json`, allow tools like bundlers and frameworks to automatically select the correct version of React—for example, switching between development and production builds or providing specialized runtimes for JSX and server environments.

## Main Entry Points

The `react` package provides different entry points for various runtimes and modes.

<x-cards data-columns="2">
  <x-card data-title="react" data-icon="lucide:box">
    The main entry point for the React library. It automatically selects the appropriate build (development or production) based on your environment.
  </x-card>
  <x-card data-title="react/jsx-runtime" data-icon="lucide:code">
    The production JSX runtime. It contains the functions that transpile JSX syntax into React function calls without development warnings.
  </x-card>
  <x-card data-title="react/jsx-dev-runtime" data-icon="lucide:terminal">
    The development JSX runtime. This version includes extra warnings and checks to help you debug your application during development.
  </x-card>
  <x-card data-title="react/compiler-runtime" data-icon="lucide:cpu">
    Provides runtime helpers required by the React Compiler. This is typically managed automatically by the compiler itself.
  </x-card>
</x-cards>

## Conditional Exports

React uses conditional exports to serve the correct code for different environments. This is most notable in the distinction between client and server environments (like React Server Components) and between development and production modes.

### Server vs. Client Environments

Many entry points have a `react-server` condition. This allows bundlers to resolve to a version of React specifically designed for server-side rendering and React Server Components, which has a different set of capabilities than the client-side version.

For a deeper dive into this topic, see the [Server vs. Client Environments](./advanced-server-vs-client.md) guide.

### Development vs. Production

React automatically switches between development and production builds based on the `process.env.NODE_ENV` variable. The entry point files typically contain logic like this:

```javascript
'use strict';

if (process.env.NODE_ENV === 'production') {
  module.exports = require('./cjs/react.production.js');
} else {
  module.exports = require('./cjs/react.development.js');
}
```

When `NODE_ENV` is set to `'production'`, you get a minified, optimized version of React. In development, you get a version with helpful warnings and debugging features.

## Summary of Package Exports

The following table provides an overview of the primary entry points available in the `react` package and their intended use cases.

| Export Path | Description | Supported Conditions |
|---|---|---|
| `.` | The main React library export. | `react-server`, `default` |
| `./jsx-runtime` | The production JSX transform runtime. | `react-server`, `default` |
| `./jsx-dev-runtime` | The development JSX transform runtime. | `react-server`, `default` |
| `./compiler-runtime` | Runtime helpers for the React Compiler. | `react-server`, `default` |
| `./package.json` | Exposes the package's `package.json` file. | N/A |
