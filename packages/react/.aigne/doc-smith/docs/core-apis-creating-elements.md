# Creating & Manipulating Elements

While [JSX](./core-apis-jsx.md) is the most common way to describe your UI, it's a convenient syntax for underlying React functions. Understanding these functions provides more direct control over React elements and is useful for certain advanced patterns. React elements are the lightweight objects that describe what you want to see on the screen.

This guide covers the primary APIs for working with them directly: `createElement`, `cloneElement`, and `isValidElement`.

## createElement()

This is the fundamental way to create a React element. Before JSX, all React UIs were written with `createElement`. When using the classic JSX transform, your JSX code is compiled directly into `React.createElement` calls.

**Syntax**

```javascript
React.createElement(type, [props], [...children])
```

**Parameters**

| Parameter | Type | Description |
|---|---|---|
| `type` | `string` \| `Component` | The element type. This can be a tag name string (e.g., `'div'`) or a React component (a class or a function). |
| `props` | `object` | An object containing the props for the element. `null` is treated as an empty object. `key` is a special reserved prop. |
| `children` | `ReactNode` | Zero or more child elements. These can be other React elements, strings, numbers, etc. |

**Example**

```javascript
import React from 'react';

// With JSX, it looks like this:
const jsxElement = <h1 className="greeting">Hello, world!</h1>;

// With React.createElement, it's equivalent to this:
const createElementElement = React.createElement(
  'h1',
  { className: 'greeting' },
  'Hello, world!'
);

// You can also nest elements:
const nestedElement = React.createElement(
  'div',
  null, // no props
  React.createElement('p', null, 'This is a paragraph inside a div.')
);
```

This shows that `createElement` is the verbose but explicit foundation upon which the convenience of JSX is built.

## cloneElement()

This utility allows you to create a new React element using another element as a starting point. It is particularly useful for modifying the props of an element, often an element passed down as `props.children`.

**Syntax**

```javascript
React.cloneElement(element, [props], [...children])
```

**Parameters**

| Parameter | Type | Description |
|---|---|---|
| `element` | `ReactElement` | The element to be cloned. This must be a valid React element. |
| `props` | `object` | New props to be shallowly merged into the original element's props. `key` and `ref` from this object will take precedence. |
| `children` | `ReactNode` | New children that will completely replace the original element's children. |

**Example**

```javascript
import React from 'react';

// A component that adds a CSS class to its direct child
function Tabbable({ children }) {
  // We can only clone a single element.
  const element = React.Children.only(children);

  if (React.isValidElement(element)) {
    return React.cloneElement(element, {
      className: `${element.props.className || ''} tabbable-item`,
      tabIndex: 0 // Make it focusable
    });
  }
  return children;
}

// Usage:
function App() {
  return (
    <Tabbable>
      <button onClick={() => alert('Clicked!')}>Click Me</button>
    </Tabbable>
  );
}
```

In this example, the `Tabbable` component takes a single child, clones it, and merges new `className` and `tabIndex` props to enhance its behavior without the child component needing to be aware of it.

## isValidElement()

This function verifies whether a given object is a React element. It provides a safe way to check if you can work with something using element-specific logic like `cloneElement`.

**Syntax**

```javascript
React.isValidElement(object)
```

**Parameters**

| Parameter | Type | Description |
|---|---|---|
| `object` | `any` | The object to be checked. |

It returns `true` if `object` is a React element, and `false` otherwise. Internally, it checks for the presence of the `$$typeof: REACT_ELEMENT_TYPE` property.

**Example**

```javascript
import React from 'react';

function Wrapper({ children }) {
  const validElementCount = React.Children.toArray(children).filter(React.isValidElement).length;

  return (
    <div>
      <p>Number of valid React Elements passed as children: {validElementCount}</p>
      <div className="content">
        {children}
      </div>
    </div>
  );
}

// Usage:
<Wrapper>
  <h1>A Title</h1>
  {'Just a string'}
  {null}
  <p>A paragraph.</p>
</Wrapper>
// Renders:
// Number of valid React Elements passed as children: 2
```

This example uses `isValidElement` to count how many of the children are actual React elements versus other primitive types like strings or `null`.

## Summary

While you will primarily use JSX in day-to-day development, understanding `createElement`, `cloneElement`, and `isValidElement` is valuable for building flexible components and libraries. These functions provide the low-level control needed to manipulate elements passed as props, enabling powerful patterns and abstractions.

Now that you understand how elements are created, learn how to interact with the underlying DOM nodes they render to by exploring [Refs](./core-apis-refs.md).