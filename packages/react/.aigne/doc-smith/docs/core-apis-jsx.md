# JSX

JSX is a syntax extension for JavaScript that allows you to write UI structures in a familiar, HTML-like syntax. It is a fundamental part of writing React components, providing a concise and readable way to describe what the UI should look like. While it may resemble a template language, JSX comes with the full power of JavaScript.

This section explores how JSX is transformed into native JavaScript that the browser can understand and details the different runtimes React provides for client and server environments.

## JSX Transformation

Browsers do not understand JSX out of the box. Your code needs to be run through a compiler (like Babel or TypeScript) to transform the JSX syntax into standard JavaScript function calls. 

Historically, this transformation would convert JSX into `React.createElement(...)` calls. The modern JSX transform, however, automatically converts JSX into special `jsx(...)` function calls, which can lead to performance improvements and simpler code without needing to import `React` into every file just for JSX.

Here is a conceptual overview of the transformation process:

```d2
direction: down

"jsx-code": {
  label: "Your JSX Code\n<MyComponent name=\"React\" />"
  shape: code
}

compiler: {
  label: "Compiler\n(Babel, TypeScript, etc.)"
  shape: rectangle
}

"runtime-functions": {
  label: "React Runtime Functions"
  shape: package
  grid-columns: 2

  "prod-runtime": {
    label: "Production Runtime"
    shape: rectangle
    "jsx()": {}
    "jsxs()": {}
  }

  "dev-runtime": {
    label: "Development Runtime"
    shape: rectangle
    "jsxDEV()": {}
  }
}

"jsx-code" -> compiler: "1. Compilation"
compiler -> "runtime-functions": "2. Output Function Calls"

```

## Environment-Specific Runtimes

React provides different runtime packages tailored for client and server environments, as well as for production and development modes. This separation allows for environment-specific optimizations and features.

You typically configure your build tool to use the correct runtime automatically. The main entry points are:

| Entry Point | Environment | Description |
|---|---|---|
| `react/jsx-runtime` | Client (Production) | The standard, optimized runtime for use in the browser. |
| `react/jsx-dev-runtime` | Client (Development) | A special runtime for development that includes extra validation and warnings. |
| `react/jsx-runtime.react-server` | Server (Production) | An optimized runtime specifically for server environments like React Server Components. |
| `react/jsx-dev-runtime.react-server`| Server (Development) | The development version of the server runtime, with added debugging features. |

## Core Runtime Functions

The modern JSX transform uses a few key functions exported by the runtimes:

*   `jsx(type, config, maybeKey)`: This is the primary function used to create React elements. It is called for elements with a single child or children that are passed dynamically.

*   `jsxs(type, config, maybeKey)`: A variation of `jsx` that is used as an optimization when an element has multiple, static children (i.e., children are defined as an array in the source code). This allows React to perform potential optimizations.

*   `jsxDEV(type, config, maybeKey, isStaticChildren)`: This is the development-only version of the function. It is instrumental for the developer experience, as it includes extensive validation and warning logic. For example, it warns about missing `key` props in lists, the misuse of the `key` prop, and provides more detailed error messages and component stack traces during development.

## Important Considerations

When working with JSX, there are a few special props and concepts to keep in mind.

### The `key` Prop

The `key` prop is a special string attribute you need to include when creating lists of elements. Keys help React identify which items have changed, are added, or are removed. They should be stable, predictable, and unique among a list of siblings.

```javascript
const todoItems = todos.map((todo) =>
  <li key={todo.id}>
    {todo.text}
  </li>
);
```

In development mode, React will warn you if you forget to provide a `key` for items in an array. Note that `key` is not a standard prop and cannot be accessed from the child component (e.g., via `props.key`).

### Fragments

A common pattern in React is for a component to return multiple elements. JSX requires a single root element. Fragments let you group a list of children without adding extra nodes to the DOM.

You can use them with the explicit `Fragment` export or the more concise `<>...</>` syntax.

```javascript
import { Fragment } from 'react';

function Columns() {
  return (
    <>
      <td>Hello</td>
      <td>World</td>
    </>
  );
}
```

### The `ref` Prop

As of React 19, `ref` is a regular prop that you can pass to a component to get a reference to a DOM element or a class component instance. Previously, accessing `element.ref` was possible but is now deprecated. You should always interact with refs via the `ref` prop.

---

Understanding the JSX transform and its runtimes provides insight into how React works under the hood. For cases where you need to programmatically create elements without using JSX, you can learn more in the [Creating & Manipulating Elements](./core-apis-creating-elements.md) guide.