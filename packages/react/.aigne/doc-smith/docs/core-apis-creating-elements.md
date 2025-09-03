# Creating & Manipulating Elements

While [JSX](./core-apis-jsx.md) is the most common and convenient way to describe your UI in React, it's important to understand that it is syntactic sugar for underlying function calls. At its core, every JSX tag is transformed into a `React.createElement()` call, which produces a JavaScript object known as a React element. This section covers the low-level APIs for creating and manipulating these elements directly.

Understanding these functions is useful for building higher-order components, designing utility libraries, or when you need to dynamically add props to children.

```d2
direction: down

"JSX": {
  shape: document
  label: "<h1 className='greeting'>Hello</h1>"
}

"Babel Compiler": {
  shape: hexagon
}

"Function Call": {
  shape: rectangle
  label: "React.createElement('h1', {className: 'greeting'}, 'Hello')"
}

"React Element Object": {
  shape: package
  label: "A lightweight description of what to render"
  "$$typeof": "Symbol(react.element)"
  "type": "'h1'"
  "props": "{ className: 'greeting', children: 'Hello' }"
}

"JSX" -> "Babel Compiler": "Transpilation"
"Babel Compiler" -> "Function Call": "Outputs"
"Function Call" -> "React Element Object": "Creates"
```

---

## `createElement()`

`React.createElement()` is the fundamental function that creates and returns a new React element of a given type. The JSX transform compiles to using this function.

`React.createElement(type, [props], [...children])`

**Parameters**

| Parameter | Type | Description |
|---|---|---|
| `type` | string \| Component | The element type. This can be a tag name string (e.g., `'div'`, `'span'`), a React component type (a class or function), or a React fragment type. |
| `props` | object | An object containing the properties (props) for the element. This object should not contain reserved props like `key`. If `config` is `null` or `undefined`, it is treated as an empty object. |
| `children` | ReactNode... | A variable number of child arguments. These can be other React elements, strings, numbers, or arrays of nodes. |

**Example**

The following JSX code:

```jsx
const element = (
  <h1 className="greeting">
    Hello, world!
  </h1>
);
```

Is equivalent to this `createElement()` call:

```javascript
const element = React.createElement(
  'h1',
  {className: 'greeting'},
  'Hello, world!'
);
```

Both versions produce the same React element object.

---

## `cloneElement()`

`React.cloneElement()` clones and returns a new React element using an existing `element` as the starting point. The resulting element will have the original element's props with the new props merged in shallowly.

`React.cloneElement(element, [props], [...children])`

**Parameters**

| Parameter | Type | Description |
|---|---|---|
| `element` | ReactElement | The React element to be cloned. Must be a valid React element. |
| `props` | object | An object containing new props to merge into the cloned element. `key` and `ref` from the original element will be preserved unless overridden. |
| `children` | ReactNode... | New children that will replace the existing `children`. |

**Usage**

`cloneElement` is primarily used to add or modify the props of children passed down to a component. This is common in layout components that need to inject props into their children.

**Example**

This component adds a `className` prop to all of its direct children that are valid React elements.

```javascript
import React from 'react';

function CustomContainer({ children }) {
  const enhancedChildren = React.Children.map(children, child => {
    if (React.isValidElement(child)) {
      // Clones the child and adds a new prop
      return React.cloneElement(child, { className: 'enhanced-child' });
    }
    return child;
  });

  return <div className="container">{enhancedChildren}</div>;
}

// Usage:
// <CustomContainer>
//   <p>First paragraph</p>
//   <span>Some text</span>
// </CustomContainer>
// Both <p> and <span> will receive the 'enhanced-child' class.
```

---

## `isValidElement()`

`React.isValidElement()` is a utility function that verifies whether an object is a React element.

`React.isValidElement(object)`

**Parameters**

| Parameter | Type | Description |
|---|---|---|
| `object` | any | The object to be checked. |

**Returns**

Returns `true` if `object` is a React element, and `false` otherwise.

**Example**

This is often used in conjunction with `React.Children.map` or other utilities to ensure you are only attempting to manipulate React elements.

```javascript
import React, { isValidElement } from 'react';

const validElement = <div />;
const componentType = () => <div />;
const notAnElement = 'Hello';
const nullValue = null;

console.log(isValidElement(validElement));     // true
console.log(isValidElement(componentType));   // false (it's a function, not an element)
console.log(isValidElement(notAnElement));    // false
console.log(isValidElement(nullValue));         // false
```

---

Now that you understand how elements are created and manipulated, you can explore how to interact with the children passed to your components. Learn more in the [Children Utilities](./core-apis-children-utilities.md) guide.