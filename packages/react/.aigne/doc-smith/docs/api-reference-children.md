# React.Children

The `React.Children` utility provides methods for working with the `props.children` data structure. In React, `props.children` is an opaque data structure, meaning you shouldn't assume it's a simple array. It can be a single element, an array of elements, a string, a number, or even `undefined`.

These utilities help you iterate, count, and transform the children prop safely without making assumptions about its underlying structure. This is particularly useful when designing reusable components that need to manipulate the elements they receive.

For more foundational concepts, you might want to review [Components & Classes](./api-reference-components.md).

## map()

Invokes a function on every direct child contained within `children` and returns an array of the results. This is similar to the native `Array.prototype.map()` method, but it correctly handles the various types that `props.children` can be.

If `children` is a `Fragment`, it will be traversed, but arrays will not be flattened. The returned array will contain the result of calling the `mapFn` on each child.

### Parameters

<x-field data-name="children" data-type="ReactNodeList" data-required="true" data-desc="The props.children object to iterate over."></x-field>
<x-field data-name="mapFn" data-type="(child: React.Node, index: number) => any" data-required="true" data-desc="The function to be called for each child. It receives the child and its index as arguments."></x-field>
<x-field data-name="context" data-type="any" data-required="false" data-desc="The 'this' context to be used when executing mapFn."></x-field>

### Returns

<x-field data-name="mappedChildren" data-type="array" data-desc="An array containing the return value of mapFn for each child."></x-field>

### Example

Here's a component that wraps each of its children in a `<li>` element.

```javascript ListWrapper Component icon=logos:javascript
import React from 'react';

function ListWrapper({ children }) {
  return (
    <ul>
      {React.Children.map(children, (child, index) => (
        <li key={index}>{child}</li>
      ))}
    </ul>
  );
}

// Usage:
// <ListWrapper>
//   <span>First Item</span>
//   <span>Second Item</span>
// </ListWrapper>
//
// Renders:
// <ul>
//   <li><span>First Item</span></li>
//   <li><span>Second Item</span></li>
// </ul>
```

## forEach()

Similar to `React.Children.map()`, but it does not return an array. It iterates through each direct child in `children`, calling the provided function for each one. Use this when you need to execute a function for each child but don't need to transform them into a new array.

### Parameters

<x-field data-name="children" data-type="ReactNodeList" data-required="true" data-desc="The props.children object to iterate over."></x-field>
<x-field data-name="forEachFunc" data-type="(child: React.Node, index: number) => void" data-required="true" data-desc="The function to be called for each child."></x-field>
<x-field data-name="context" data-type="any" data-required="false" data-desc="The 'this' context to be used when executing forEachFunc."></x-field>

### Returns

This method does not return a value.

### Example

This example logs the `displayName` or `type` of each child element.

```javascript ChildLogger Component icon=logos:javascript
import React from 'react';

function ChildLogger({ children }) {
  React.Children.forEach(children, (child, index) => {
    if (React.isValidElement(child)) {
      console.log(`Child #${index} is a <${child.type.displayName || child.type}> element.`);
    }
  });

  return <div>{children}</div>;
}
```

## count()

Returns the total number of components in `children`. This is equivalent to the number of times a callback passed to `map()` or `forEach()` would be invoked.

### Parameters

<x-field data-name="children" data-type="ReactNodeList" data-required="true" data-desc="The props.children object to count."></x-field>

### Returns

<x-field data-name="count" data-type="number" data-desc="The number of child components."></x-field>

### Example

```javascript ItemCounter Component icon=logos:javascript
import React from 'react';

function ItemCounter({ children }) {
  const childCount = React.Children.count(children);
  return (
    <div>
      <h3>There are {childCount} items.</h3>
      {children}
    </div>
  );
}
```

## toArray()

Returns the `children` data structure as a flat array. This is useful when you need to treat the `children` prop as a standard array for operations like sorting or slicing, while preserving the necessary keys for rendering.

### Parameters

<x-field data-name="children" data-type="ReactNodeList" data-required="true" data-desc="The props.children object to convert to an array."></x-field>

### Returns

<x-field data-name="childrenArray" data-type="React.Node[]" data-desc="A flattened array of React nodes with keys assigned."></x-field>

### Example

This component converts its children to an array and renders them in reverse order.

```javascript ReversedList Component icon=logos:javascript
import React from 'react';

function ReversedList({ children }) {
  const childArray = React.Children.toArray(children);
  // Now we can safely use array methods
  const reversedChildren = childArray.reverse();

  return <>{reversedChildren}</>;
}

// Usage:
// <ReversedList>
//   <p>One</p>
//   <p>Two</p>
//   <p>Three</p>
// </ReversedList>
//
// Renders:
// <p>Three</p>
// <p>Two</p>
// <p>One</p>
```

## only()

Verifies that `children` has only one child (which must be a valid React element) and returns it. If `children` is empty, `null`, `undefined`, or contains more than one child, this function will throw an error.

This is useful as a validation for components that are designed to wrap a single child element.

### Parameters

<x-field data-name="children" data-type="any" data-required="true" data-desc="The props.children object to check."></x-field>

### Returns

<x-field data-name="child" data-type="ReactElement" data-desc="The single React element child."></x-field>

### Example

This `<Frame>` component requires a single child to render within its border.

```javascript Frame Component icon=logos:javascript
import React from 'react';

function Frame({ children }) {
  // This will throw an error if more than one child is passed.
  const singleChild = React.Children.only(children);

  return (
    <div style={{ border: '2px solid blue', padding: '10px' }}>
      {singleChild}
    </div>
  );
}

// Valid usage:
// <Frame><p>Hello</p></Frame>

// Invalid usage (throws error):
// <Frame><p>One</p><p>Two</p></Frame>
// <Frame />
```