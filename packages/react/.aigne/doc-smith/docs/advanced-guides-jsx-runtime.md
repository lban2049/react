# JSX Runtime

The modern React JSX transform automatically converts JSX code into regular JavaScript function calls without needing `React` to be in scope. This guide details the functions it uses and how they work under the hood.

Traditionally, JSX was compiled into `React.createElement(...)` calls. The new transform, however, compiles JSX to `jsx(...)` function calls, which can lead to performance optimizations and simplifies the file structure. For more background, you can read about the [new JSX transform](https://react.dev/link/new-jsx-transform).

## Core Runtime Exports

The JSX runtime provides a few key functions that are used by the compiler. You generally don't import or use these directly, but understanding them is helpful for advanced use cases or debugging.

| Function | Package | Description |
|---|---|---|
| `jsx` | `react/jsx-runtime` | Creates a React element. Used for elements with a single child or dynamic children. |
| `jsxs` | `react/jsx-runtime` | An optimized version of `jsx` for elements with multiple, static children (i.e., children are passed as an array). |
| `jsxDEV` | `react/jsx-dev-runtime` | A development-only version of `jsx` that includes extra validation, warnings (like missing `key` props in lists), and debugging information. |
| `Fragment` | `react/jsx-runtime` | A component that lets you group a list of children without adding extra nodes to the DOM. Corresponds to the `<>...</>` syntax. |

## How It Works

A build tool with the new JSX transform (like modern versions of Babel or TypeScript) will process your JSX source code and output standard JavaScript.

### Single Element Example

For a simple element, the compiler uses the `jsx` function.

```jsx JSX Source Code icon=logos:react
const element = <h1 className="greeting">Hello, world!</h1>;
```

This code is compiled into:

```javascript Compiled Output icon=logos:javascript
import { jsx } from 'react/jsx-runtime';

const element = jsx('h1', { className: 'greeting', children: 'Hello, world!' });
```

### Multiple Children Example

For elements with multiple static children, the compiler may use the `jsxs` function as an optimization.

```jsx JSX Source Code icon=logos:react
const element = (
  <div>
    <span>Hello</span>
    <span>World</span>
  </div>
);
```

This code is compiled into:

```javascript Compiled Output icon=logos:javascript
import { jsxs } from 'react/jsx-runtime';
import { jsx } from 'react/jsx-runtime';

const element = jsxs('div', {
  children: [
    jsx('span', { children: 'Hello' }),
    jsx('span', { children: 'World' })
  ]
});
```

## Environment-Specific Runtimes

React provides two distinct JSX runtime packages to support different environments:

- **`react/jsx-runtime`**: This is the production runtime. It's optimized for performance and doesn't include the warnings and debugging aids found in the development version. Both `jsx` and `jsxs` map to the lightweight `jsxProd` implementation.

- **`react/jsx-dev-runtime`**: This is the development runtime. It's used during development to provide helpful warnings and more detailed error messages. For example, it will warn you if you forget to add a `key` to elements in an array. This runtime exports `jsxDEV`, a more robust function that gathers debugging information.

Your build setup should automatically select the correct runtime based on the `NODE_ENV` environment variable.

## Function Signatures

The core functions of the JSX runtime have the following signatures.

### `jsx(type, config, maybeKey)`

This is the primary function for creating elements in production.

<x-field data-name="type" data-type="string | Function | class" data-required="true" data-desc="The element type. For intrinsic elements like 'div', this is a string. For custom components, this is a function or class."></x-field>
<x-field data-name="config" data-type="object" data-required="true" data-desc="An object containing all props passed to the element. The 'children' prop, if any, is included in this object."></x-field>
<x-field data-name="maybeKey" data-type="string | number" data-required="false" data-desc="An optional key, passed as the third argument if a key is explicitly provided in the JSX. If the key is spread into props (e.g., {...{key: '123'}}), it will be extracted from the 'config' object."></x-field>


### `jsxDEV(type, config, maybeKey, isStaticChildren)`

This is the development-only version used for creating elements, providing additional validation.

<x-field data-name="type" data-type="string | Function | class" data-required="true" data-desc="The element type, same as in jsx."></x-field>
<x-field data-name="config" data-type="object" data-required="true" data-desc="The props object, same as in jsx."></x-field>
<x-field data-name="maybeKey" data-type="string | number" data-required="false" data-desc="The optional key, same as in jsx."></x-field>
<x-field data-name="isStaticChildren" data-type="boolean" data-required="true" data-desc="A boolean flag passed by the compiler indicating if the children are a static array. This helps React perform optimizations and validations."></x-field>


## Comparison with `createElement`

The modern JSX runtime supersedes the classic `React.createElement` transform. The key differences are:

1.  **Scope**: `React.createElement` requires `React` to be imported in every file that uses JSX. The new runtime does not.
2.  **Arguments**: `createElement` takes children as a variable number of arguments after the `props` object. The `jsx` functions expect `children` to be a property within the `props` (`config`) object.
3.  **Performance**: By passing props as a single object and having dedicated `jsxs` and `jsxDEV` functions, the new runtime can be more efficient and provide better development-time feedback.

While `createElement` is still part of React's top-level API for backward compatibility and manual element creation, the JSX transform no longer compiles to it by default.

---

Understanding the JSX runtime provides insight into how React works at a fundamental level. For more advanced topics, you might be interested in the experimental [React Compiler Runtime](./advanced-guides-compiler-runtime.md).