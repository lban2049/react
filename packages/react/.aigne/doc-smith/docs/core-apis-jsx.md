# JSX

JSX is a syntax extension for JavaScript that lets you write UI structures in a syntax that resembles HTML. While it might remind you of a template language, it comes with the full power of JavaScript. JSX provides a concise and familiar way to define React elements.

For a deeper dive into the design and specification, see the original proposal: [RFC: New JSX Transform](https://github.com/reactjs/rfcs/pull/107).

## The JSX Transform

Browsers do not understand JSX out of the box. It needs to be compiled into standard JavaScript by a tool like Babel or TypeScript. The modern JSX transform converts JSX into calls to special functions without needing to import `React` into the scope of every file.

For example, this JSX code:

```jsx
const element = (
  <div className="greeting">
    <h1>Hello, World!</h1>
  </div>
);
```

Is compiled into this JavaScript object using the `jsxs` function:

```javascript
// Compiled output
import {jsxs as _jsxs} from 'react/jsx-runtime';
import {jsx as _jsx} from 'react/jsx-runtime';

const element = _jsxs('div', {
  className: 'greeting',
  children: [
    _jsx('h1', { children: 'Hello, World!' })
  ]
});
```

This compiled output is what React uses to create the actual DOM elements and manage the UI.

## JSX Runtimes

React provides different JSX runtimes tailored for specific environments and purposes. Your build tooling will automatically select the appropriate one, but it's useful to understand the distinction.

| Runtime Package                  | Purpose                                                              |
|----------------------------------|----------------------------------------------------------------------|
| `react/jsx-runtime`              | The production runtime for client-side environments.                 |
| `react/jsx-dev-runtime`          | The development runtime for client-side environments.                |
| `react/jsx-runtime.react-server` | The production runtime for server environments (like RSC).           |
| `react/jsx-dev-runtime.react-server` | The development runtime for server environments.                     |

### Production vs. Development Runtimes

The primary difference lies in the level of validation and debugging information provided.

- **Production (`jsx`, `jsxs`)**: These functions are optimized for performance. They create the `ReactElement` object with the minimal properties needed: `$$typeof`, `type`, `key`, `ref`, and `props`.

- **Development (`jsxDEV`)**: This function includes extensive validation and warnings to help you catch potential issues during development. For instance, `jsxDEV` will:
  - Validate that children in an array have a unique `key` prop.
  - Warn you if you spread a `key` prop from another object, as keys must be passed directly.
  - Attach debugging information like `_owner`, `_debugStack`, and `_debugTask` to the element object for better error messages and developer tools integration.

## Core JSX Functions

The JSX transform uses a few key functions to create elements.

### `jsx` and `jsxs`

The compiler automatically chooses between `jsx` and `jsxs` for optimization:
- `jsx`: Used for elements with a single child or children that are dynamically generated (e.g., from an array map).
- `jsxs`: Used for elements with multiple, static children. This allows React to perform optimizations by knowing the children are a static array.

```jsx
// Compiles to a call to jsx()
const singleChild = <div>{name}</div>;

// Compiles to a call to jsxs()
const multipleChildren = (
  <div>
    <p>First item</p>
    <p>Second item</p>
  </div>
);
```

### `Fragment`

React Fragments let you group a list of children without adding extra nodes to the DOM. They are useful for returning multiple elements from a component.

JSX has a shorthand syntax for Fragments (`<>...</>`):

```jsx
function UserInfo() {
  return (
    <>
      <h2>User Name</h2>
      <p>User Description</p>
    </>
  );
}

// The above is equivalent to:
import { Fragment } from 'react/jsx-runtime';

function UserInfo() {
  return (
    <Fragment>
      <h2>User Name</h2>
      <p>User Description</p>
    </Fragment>
  );
}
```

## Special Prop Handling: `key`

The `key` prop is special. It's used by React as a hint to identify which items have changed, are added, or are removed in a list of elements. 

**Important**: `key` is not passed down to your component as a prop. If you need the `key` value inside your component, you must pass it as a different prop.

During development, React will warn you if you attempt to access `this.props.key`.

```javascript
// In development, this function is defined to warn about accessing `key`.
function defineKeyPropWarningGetter(props, displayName) {
  const warnAboutAccessingKey = function () {
    // ... (warning logic)
    console.error(
      '%s: `key` is not a prop. Trying to access it will result ' +
      'in `undefined` being returned. If you need to access the same ' +
      'value within the child component, you should pass it as a different ' +
      'prop. (https://react.dev/link/special-props)',
      displayName,
    );
  };
  // ... (Object.defineProperty logic)
}
```

---

Now that you understand how JSX works under the hood, learn how to combine it with JavaScript logic to build reusable UI elements in [Components & Props](./core-apis-components-and-props.md).
