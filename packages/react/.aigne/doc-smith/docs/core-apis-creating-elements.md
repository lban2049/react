# Creating & Manipulating Elements

While you will typically use [JSX](./core-apis-jsx.md) to define your user interface, it's important to understand that JSX is syntactic sugar for a set of underlying functions that create and manage React elements. These functions provide a more direct way to interact with React's core rendering mechanism and are essential for building reusable components and advanced patterns.

A React element is a light, stateless, immutable object that describes what you want to see on the screen. It's a plain JavaScript object, not a component instance or a DOM node. React reads these objects and uses them to construct the DOM and keep it up to date.

This guide covers the primary APIs for creating and manipulating these element objects directly.

## `createElement()`

`React.createElement()` is the fundamental building block that JSX compiles down to. It creates and returns a new React element of the given type.

### Syntax

```javascript React.createElement() icon=logos:react
React.createElement(type, [props], [...children])
```

### Parameters

| Parameter | Type | Description |
|---|---|---|
| `type` | `string \| function \| class` | The element type. This can be a tag name string (e.g., `'div'`), a React component type (a function or a class), or a React fragment type. |
| `props` | `object` | An object containing the props for the element. `key` is a special property that should be included here if needed. |
| `...children` | `ReactNode` | The children of the element. You can pass multiple children as subsequent arguments, and they will be available in `props.children`. |

### Example

Here's how you would create a simple heading element with a child `span`.

```javascript icon=logos:react
import React from 'react';

const greetingElement = React.createElement(
  'h1',
  { className: 'greeting' },
  'Hello, ',
  React.createElement('span', { style: { color: 'blue' } }, 'world!')
);

// This is equivalent to the following JSX:
// <h1 className="greeting">Hello, <span style={{ color: 'blue' }}>world!</span></h1>
```

## `cloneElement()`

`React.cloneElement()` clones and returns a new React element using an existing element as the starting point. The resulting element will have the original element’s props with the new props merged in shallowly. New children will replace existing children.

This is particularly useful for adding or modifying the props of elements passed to your components, such as `props.children`.

### Syntax

```javascript React.cloneElement() icon=logos:react
React.cloneElement(element, [props], [...children])
```

### Parameters

| Parameter | Type | Description |
|---|---|---|
| `element` | `ReactElement` | The React element to be cloned. |
| `props` | `object` | New props to be merged into the cloned element's existing props. The `key` and `ref` from the original element will be preserved. |
| `...children` | `ReactNode` | New children that will replace the original element's `children` prop. |

### Example

This example shows a component that wraps its child and adds a new CSS class.

```javascript icon=logos:react
import React from 'react';

function AddWrapper({ children }) {
  // React.Children.only ensures that children has only one child.
  const child = React.Children.only(children);
  
  // Clone the child element and add a new class name
  const childWithClass = React.cloneElement(child, {
    className: `${child.props.className || ''} special-wrapper`
  });

  return <div>{childWithClass}</div>;
}

// When used like this:
// <AddWrapper>
//   <p className="original">Some text</p>
// </AddWrapper>

// It will render:
// <div>
//   <p class="original special-wrapper">Some text</p>
// </div>
```

## `isValidElement()`

`React.isValidElement()` is a utility function that verifies whether an object is a React element. It returns `true` if the object is a valid element and `false` otherwise.

### Syntax

```javascript React.isValidElement() icon=logos:react
React.isValidElement(object)
```

### Parameters

| Parameter | Type | Description |
|---|---|---|
| `object` | `any` | The object to be checked. |

### Example

This is useful for safely rendering props that may or may not be a React element.

```javascript icon=logos:react
import React from 'react';

function Card({ header, content }) {
  return (
    <div className="card">
      <div className="card-header">
        {React.isValidElement(header) ? header : <h2>{header}</h2>}
      </div>
      <div className="card-content">
        {content}
      </div>
    </div>
  );
}

// Usage:
// <Card header={<h1>Custom Header Element</h1>} content="..." />
// <Card header="Simple Header String" content="..." />
```

## Next Steps

Understanding these core functions gives you deeper insight into how React works under the hood. With this knowledge, you are better equipped to build complex and flexible components.

<x-cards>
  <x-card data-title="Refs" data-icon="lucide:anchor" data-href="/core-apis/refs">
    Learn how to access and interact with the DOM nodes that React elements render into.
  </x-card>
  <x-card data-title="Children Utilities" data-icon="lucide:list-tree" data-href="/core-apis/children-utilities">
    Explore the React.Children API for working with the props.children data structure, which often involves using cloneElement.
  </x-card>
</x-cards>