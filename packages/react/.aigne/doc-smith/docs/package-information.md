# Package Information

This section provides essential metadata about the `react` npm package. The information is sourced directly from its `package.json` file, offering a quick reference for developers on versioning, licensing, and important links.

## General Information

| Key         | Value                                                              |
| ----------- | ------------------------------------------------------------------ |
| **Name**    | `react`                                                            |
| **Version** | `19.1.0`                                                           |
| **Description** | React is a JavaScript library for building user interfaces.        |
| **License** | `MIT`                                                              |
| **Homepage**| <a href="https://react.dev/" target="_blank">https://react.dev/</a>                                                   |
| **Repository**| <a href="https://github.com/facebook/react.git" target="_blank">https://github.com/facebook/react.git</a>                         |
| **Bug Tracker** | <a href="https://github.com/facebook/react/issues" target="_blank">https://github.com/facebook/react/issues</a>                   |
| **Main Entry Point** | `index.js`                                                         |
| **Node Engine** | `>=0.10.0`                                                         |

## Published Files

The following files and directories are included when the `react` package is published to npm, defining its distributable contents.

| File / Directory                  |
| --------------------------------- |
| `LICENSE`                         |
| `README.md`                       |
| `index.js`                        |
| `cjs/`                            |
| `compiler-runtime.js`             |
| `jsx-runtime.js`                  |
| `jsx-runtime.react-server.js`     |
| `jsx-dev-runtime.js`              |
| `jsx-dev-runtime.react-server.js` |
| `react.react-server.js`           |

## Module Exports

The `react` package uses the `exports` field in `package.json` to define its public API for different environments, such as standard module resolution and React Server Components environments. This ensures that the correct files are loaded based on the context.

| Entry Point          | Default Environment             | React Server Environment            |
| -------------------- | ------------------------------- | ----------------------------------- |
| `.`                  | `./index.js`                    | `./react.react-server.js`           |
| `./jsx-runtime`      | `./jsx-runtime.js`              | `./jsx-runtime.react-server.js`     |
| `./jsx-dev-runtime`  | `./jsx-dev-runtime.js`          | `./jsx-dev-runtime.react-server.js` |
| `./compiler-runtime` | `./compiler-runtime.js`         | `./compiler-runtime.js`             |
| `./package.json`     | `./package.json`                | Not specified                       |
| `./src/*`            | `./src/*`                       | Not specified                       |