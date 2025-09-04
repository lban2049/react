# Creating & Manipulating Elements

While [JSX](./core-apis-jsx.md) is the most common and convenient way to describe your UI, it's essentially syntactic sugar for a set of underlying functions that create React elements. Understanding these functions gives you more direct control over element creation and is essential for certain advanced patterns and for building tooling. This section covers the core utilities for working directly with React elements.

The primary APIs for this are `React.createElement()`, `React.cloneElement()`, and the `React.isValidElement()` helper.

## `createElement()`

The `createElement()` function is the original building block of React UIs. Before the new JSX transform, every JSX tag was compiled into a `React.createElement()` call.

It creates and returns a new React element object with the specified properties. This object is a lightweight description of what you want to render.

### Syntax

```javascript
React.createElement(type, [props], [...children])
```

### Parameters

| Parameter | Type | Description |
|---|---|---|
| `type` | string \| Component | The element type. Can be a tag name string (e.g., `'div'`, `'span'`), a React component type (a class or a function), or a React fragment type. |
| `props` | object | An object containing the props for the element. `key` is a special prop that is handled separately. This argument can be `null` or omitted if there are no props. |
| `children` | ReactNode | One or more child elements. These are passed as subsequent arguments after `props`. |

### Example

Here is how you would create a simple heading element using `createElement()`, and its JSX equivalent.

```javascript
import React from 'react';

// Using createElement to create an h1 element
const greeting = React.createElement(
  'h1',
  { className: 'site-title' },
  'Hello, World!'
);

// The code above is equivalent to this JSX:
// const greeting = <h1 className="site-title">Hello, World!</h1>;
```

When you have multiple children, you pass them as additional arguments:

```javascript
const list = React.createElement(
  'ul',
  null, // No props
  React.createElement('li', null, 'Item 1'),
  React.createElement('li', null, 'Item 2')
);

// Equivalent JSX:
// <ul>
//   <li>Item 1</li>
//   <li>Item 2</li>
// </ul>
```

## `cloneElement()`

The `cloneElement()` function allows you to clone an existing React element and merge new props into it. This is particularly useful for manipulating elements passed down as `props.children`.

### Syntax

```javascript
React.cloneElement(element, [props], [...children])
```

### Parameters

| Parameter | Type | Description |
|---|---|---|
| `element` | ReactElement | The React element to be cloned. |
| `props` | object | An object containing new props to be merged into the cloned element's existing props. The original element's `key` and `ref` are preserved. |
| `children` | ReactNode | New children that will completely replace the original element's children. |

### Example

Imagine you have a component that needs to add a specific `className` to any child passed to it.

```javascript
import React from 'react';

function HighlightWrapper({ children }) {
  // Clone the child element and add a new className
  const childWithClass = React.cloneElement(children, {
    className: `${children.props.className || ''} highlighted`
  });

  return <div>{childWithClass}</div>;
}

// Usage
function App() {
  return (
    <HighlightWrapper>
      <p className="text-normal">This text will be highlighted.</p>
    </HighlightWrapper>
  );
}
```

In this example, `cloneElement` takes the original `<p>` element, and returns a new `<p>` element with the `highlighted` class added to its `className` prop.

## `isValidElement()`

`isValidElement()` is a utility function to verify whether a variable holds a valid React element. It checks if the object has the `$$typeof: REACT_ELEMENT_TYPE` property.

### Syntax

```javascript
React.isValidElement(object)
```

### Parameters

| Parameter | Type | Description |
|---|---|---|
| `object` | any | The object to be checked. |

### Returns

Returns `true` if `object` is a React element, and `false` otherwise.

### Example

This is useful in helper functions or components where you need to ensure you're working with a renderable element before attempting to manipulate it.

```javascript
import React, { isValidElement } from 'react';

function renderContent(content) {
  if (isValidElement(content)) {
    // It's a React element, so we can render it directly
    return content;
  } else if (typeof content === 'string') {
    // It's a string, wrap it in a p tag
    return <p>{content}</p>;
  } else {
    // It's something else, return null or a default
    return null;
  }
}

console.log(isValidElement(<div />)); // true
console.log(isValidElement('Hello')); // false
console.log(isValidElement({})); // false
```

## Under the Hood: Modern JSX Runtimes

While `createElement` is fundamental, modern React projects typically use a newer JSX transform. Instead of compiling JSX to `React.createElement`, the new transform uses functions like `jsx` and `jsxs` from a special `react/jsx-runtime` entry point. This optimization avoids the need to import `React` into every file that uses JSX.

Even with this new transform, the output is still a React element object. The core principles remain the same: your code describes the UI, and React creates element objects to represent it. The functions discussed here, especially `cloneElement` and `isValidElement`, remain relevant for direct element manipulation regardless of the JSX transform used.

---

Now that you understand how to work with elements at a low level, the next logical step is to learn how to interact with the actual DOM nodes they render. Proceed to the [Refs](./core-apis-refs.md) section to learn more.
