# JSX

JSX is a syntax extension for JavaScript that allows you to write UI descriptions in a format that resembles HTML. It provides a concise and familiar way to define React elements and components, making your code more readable and maintainable.

While JSX may look like a template language, it is fully powered by JavaScript. It gets compiled into regular JavaScript function calls by a tool like Babel. This means you can use the full power of JavaScript—including variables, loops, and conditional logic—directly within your UI markup.

For a deeper look at the underlying functions that JSX compiles to, see the guide on [Creating & Manipulating Elements](./core-apis-creating-elements.md).

## The JSX Transform

Modern React versions use a new JSX Transform that automatically imports the necessary functions from the React package, so you no longer need to import `React` into every file that uses JSX. The transform converts JSX into direct calls to `jsx` or `jsxs` functions.

Here's how a simple JSX element is transformed:

**Before Compilation:**
```jsx
const element = <h1 className="greeting">Hello, world!</h1>;
```

**After Compilation:**
```javascript
import { jsx as _jsx } from 'react/jsx-runtime';

const element = _jsx('h1', { className: 'greeting', children: 'Hello, world!' });
```

This compilation step turns your declarative JSX into concrete `ReactElement` objects that React can use to build the DOM.

```d2
direction: down

"JSX in your code": {
  shape: document
  "const element = <MyComponent name='React' />"
}

"Build Tool (e.g., Babel)": {
  shape: hexagon
}

"JavaScript Function Call": {
  shape: document
  "jsx(MyComponent, { name: 'React' })"
}

"React Runtime": {
  shape: package
  "ReactElement() factory"
}

"React Element Object": {
  shape: stored_data
  "{ $$typeof: REACT_ELEMENT_TYPE, type: MyComponent, ... }"
}

"Rendered UI": {
   shape: rectangle
   "The actual component displayed on the screen"
}

"JSX in your code" -> "Build Tool (e.g., Babel)": "Transpilation"
"Build Tool (e.g., Babel)" -> "JavaScript Function Call": "Outputs"
"JavaScript Function Call" -> "React Runtime": "Invokes"
"React Runtime" -> "React Element Object": "Creates"
"React Element Object" -> "Rendered UI": "Used by React to render"
```

## JSX Runtimes

React provides different JSX runtimes tailored for specific environments and modes (development vs. production). Your build tooling will automatically select the correct one based on your configuration.

| Runtime | Purpose |
|---|---|
| `react/jsx-runtime` | The main production runtime for client-side applications. It is optimized for performance. |
| `react/jsx-dev-runtime` | The development runtime for client-side applications. It includes additional checks, validations, and helpful warnings. |
| `react/jsx-runtime/server` | The production runtime specifically for server environments, such as when using React Server Components. |
| `react/jsx-dev-runtime/server`| The development runtime for server environments, providing server-specific warnings and debugging information. |

These entry points export the core functions that power JSX.

## Core JSX Functions

While you typically won't call these functions directly, understanding them helps clarify how JSX works.

<x-cards data-columns="3">
  <x-card data-title="jsx()" data-icon="lucide:code">
    Used to create a React Element with a single child or dynamic children. The compiler uses this for elements like `<div>{name}</div>`.
  </x-card>
  <x-card data-title="jsxs()" data-icon="lucide:codesandbox">
    An optimized version for creating elements with multiple, static children. The compiler uses this for elements like `<div><p>First</p><p>Second</p></div>`, marking the children array as static for potential optimizations.
  </x-card>
  <x-card data-title="jsxDEV()" data-icon="lucide:bug">
    The development-only version. It validates props, checks for missing `key` props in arrays, and provides other essential warnings to catch potential bugs during development.
  </x-card>
</x-cards>

## Special Props: `key` and `ref`

Certain props have special meaning in JSX and are handled differently by React.

### The `key` Prop

The `key` prop is a special string attribute you need to include when creating lists of elements. Keys help React identify which items have changed, are added, or are removed, which is crucial for efficient updates and maintaining state in lists.

- **Keys must be unique** among siblings.
- **Keys are not passed as a prop** to your components. If you need the same value in your component, you must pass it as a different prop (e.g., `<Profile key={user.id} id={user.id} />`).

In development mode, attempting to access `props.key` within a component will result in a warning:

> `key` is not a prop. Trying to access it will result in `undefined` being returned. If you need to access the same value within the child component, you should pass it as a different prop.

### The `ref` Prop

The `ref` prop is used to get direct access to a DOM element or a class component instance. Unlike most props, `ref` is handled by React and is not passed to the component's props object.

In React 19, `ref` is passed as a regular prop. However, accessing `element.ref` is deprecated and will trigger a warning in development mode to encourage the modern prop-based approach.

---

Now that you understand how JSX works behind the scenes, you're ready to see how it's used to build reusable UIs. Continue to the [Components & Props](./core-apis-components-and-props.md) guide to learn more.