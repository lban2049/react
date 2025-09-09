# JSX

JSX is a syntax extension for JavaScript that allows you to write UI structures, which look similar to HTML, directly within your JavaScript code. It's a key feature of React that makes component rendering declarative and easy to read. While it might look like a template language, JSX is fully powered by JavaScript and gets compiled into regular JavaScript function calls that create React elements.

For a deeper look into the elements that JSX produces, see the [Creating & Manipulating Elements](./core-apis-creating-elements.md) guide.

## The JSX Transform

Modern React toolchains use a new JSX Transform that automatically converts JSX code into function calls without requiring you to import `React` into every file. This simplifies component authoring.

For example, this JSX code:

```javascript A Simple JSX Component icon=logos:react
const Greeting = () => {
  return <h1>Hello, World!</h1>;
};
```

Is transformed by the compiler into this:

```javascript Compiled Output icon=logos:javascript
import { jsx as _jsx } from 'react/jsx-runtime';

const Greeting = () => {
  return _jsx('h1', { children: 'Hello, World!' });
};
```

Notice how `_jsx` is imported from `react/jsx-runtime`. This automatic import is handled by your build tool (like Babel or SWC), and the specific file it imports from depends on the environment.

## JSX Runtimes

React provides different JSX runtime entry points tailored for specific environments. Your build configuration will automatically select the correct one. This separation ensures that development builds include helpful warnings and debugging tools, while production builds are optimized for performance.

| Entry Point                       | Environment            | Purpose                                                       |
|-----------------------------------|------------------------|---------------------------------------------------------------|
| `react/jsx-runtime`               | Production (Client)    | The standard, optimized runtime for client-side applications. |
| `react/jsx-dev-runtime`           | Development (Client)   | A client-side runtime that includes extra validations and warnings. |
| `react/jsx-runtime.react-server`  | Production (Server)    | An optimized runtime for server-only environments like RSC.     |
| `react/jsx-dev-runtime.react-server`| Development (Server)   | The development version of the server runtime with warnings.    |

These different runtimes allow React to provide environment-specific features without bloating production bundles. To learn more about the architectural differences, see our advanced guide on [Server vs. Client Environments](./advanced-server-vs-client.md).

## Core JSX Functions

The JSX transform primarily uses three functions to create elements:

<x-cards data-columns="3">
  <x-card data-title="jsx()" data-icon="lucide:box">
    Used for elements with a single child or dynamically generated children (e.g., from an array map).
  </x-card>
  <x-card data-title="jsxs()" data-icon="lucide:boxes">
    An optimization for elements with multiple, static children. The compiler passes them as a static array, enabling potential performance improvements.
  </x-card>
  <x-card data-title="jsxDEV()" data-icon="lucide:wrench">
    The development-only version. It includes extensive validation, such as checking for unique keys in lists and warning against improper prop usage.
  </x-card>
</x-cards>

The `jsxDEV` function is particularly useful during development. For instance, it validates that children in an array have a unique `key` prop and warns you if you spread a `key` from a props object instead of passing it directly.

```javascript jsxDEV Warning Example icon=logos:react
// In development, this code will produce a warning because the key
// is being spread from a props object.
const props = { key: 'unique-id', text: 'Hello' };
const element = <div {...props} />; 

// Correct usage:
const elementWithKey = <div key={props.key} {...props} />;
```

## Special Props: `key` and `ref`

Two props, `key` and `ref`, are reserved by React and have special meaning. They are used by React's reconciliation algorithm and for DOM access, respectively. They are not passed down to the component as part of the `props` object.

- **`key`**: A stable identifier for elements in a list. It helps React identify which items have changed, are added, or are removed.
- **`ref`**: Provides a way to access DOM nodes or React components created in the render method.

In development mode, React will issue a warning if you attempt to access `props.key` from within a child component, guiding you to pass the value via a different prop if needed.

---

Now that you understand how JSX syntax is transformed into function calls, you can explore the objects these functions create. Continue to the [Creating & Manipulating Elements](./core-apis-creating-elements.md) guide to learn more about `ReactElement`.