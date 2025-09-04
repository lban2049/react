# Children Utilities

In React, `props.children` is a special prop that allows components to be composed. While it often appears to be an array of React elements, it is an opaque data structure. This means you should not assume it's an array and use methods like `children.map()` directly, as `props.children` can be a single element, a string, a number, or undefined.

To work with `props.children` safely and predictably, React provides a set of utilities on the `React.Children` object. These helpers allow you to iterate over, count, and transform the children prop regardless of its underlying structure.

```d2
direction: down

props-children: {
  label: "props.children\n(Opaque Data Structure)"
  shape: package

  child-1: {
    label: "Single Element\n<div />"
    shape: rectangle
  }
  child-2: {
    label: "Array of Elements\n[<p />, <span />]"
    shape: rectangle
  }
  child-3: {
    label: "String or Number\n'Hello'"
    shape: rectangle
  }
}

react-children-api: {
  label: "React.Children Utilities\n(.map, .forEach, .toArray)"
  shape: hexagon
}

processed-array: {
  label: "Predictable Array\n(Flat, with stable keys)"
  shape: package

  element-1: {
    label: "<div key='...' />"
    shape: rectangle
  }
  element-2: {
    label: "<p key='...' />"
    shape: rectangle
  }
  element-3: {
    label: "<span key='...' />"
    shape: rectangle
  }
  element-4: {
    label: "'Hello'"
    shape: rectangle
  }
}

props-children -> react-children-api: "Processes"
react-children-api -> processed-array: "Returns"
```

## `React.Children.map`

`React.Children.map(children, function(child, index), [thisArg])`

Invokes a function on every immediate child contained within `children`. It functions much like the standard `Array.prototype.map()` method but is safe for any type of `props.children`. The returned value is an array of the results from the callback function.

A key feature of `React.Children.map` is that it automatically handles the `key` prop for each element in the returned array, which is essential for efficient rendering and maintaining state during updates.

**Example: Wrapping each child in a list item**
```jsx
import { Children } from 'react';

function List({ children }) {
  return (
    <ul>
      {Children.map(children, (child) => (
        <li>{child}</li>
      ))}
    </ul>
  );
}

function App() {
  return (
    <List>
      <span>Apple</span>
      <span>Banana</span>
      <span>Orange</span>
    </List>
  );
}
```

## `React.Children.forEach`

`React.Children.forEach(children, function(child, index), [thisArg])`

Similar to `React.Children.map()`, but it does not return an array. It is used for iterating over children without transforming them, such as for logging or other side effects.

**Example: Logging the type of each child**
```jsx
import { Children } from 'react';

function ChildLogger({ children }) {
  Children.forEach(children, (child, index) => {
    // Note: child.type might not exist for non-element children like strings.
    if (child && child.type) {
      console.log(`Child at index ${index} is a`, child.type);
    }
  });

  return <div>{children}</div>;
}

function App() {
  return (
    <ChildLogger>
      <p>First</p>
      <div />
    </ChildLogger>
  );
}
```

## `React.Children.count`

`React.Children.count(children)`

Returns the total number of components in `children`, equal to the number of times that a callback passed to `map` or `forEach` would be invoked.

**Example: Displaying a count of items**
```jsx
import { Children } from 'react';

function ItemCounter({ children }) {
  const count = Children.count(children);
  return <h3>There are {count} items.</h3>;
}

function App() {
  return (
    <ItemCounter>
      <div />
      <div />
    </ItemCounter>
  );
}
```

## `React.Children.toArray`

`React.Children.toArray(children)`

Returns the `children` opaque data structure as a flat array with keys assigned to each child. This is useful if you want to manipulate the collection of children, such as reordering or slicing them, before rendering.

**Example: Reversing and rendering children**
```jsx
import { Children } from 'react';

function ReversedList({ children }) {
  const childArray = Children.toArray(children);
  
  // Reverse the array and render it
  return <div>{childArray.reverse()}</div>;
}

function App() {
  return (
    <ReversedList>
      <span>One</span>
      <span>Two</span>
      <span>Three</span>
    </ReversedList>
  );
}
```

## `React.Children.only`

`React.Children.only(children)`

Verifies that `children` has only one child (a React element) and returns it. If `children` is not a single React element, this function will throw an error. This is useful for creating components that are designed to wrap a single child element.

**Example: A component that requires a single child**
```jsx
import { Children } from 'react';

function SingleChildWrapper({ children }) {
  // This will throw an error if more than one child is passed.
  const child = Children.only(children);
  
  // You can now safely clone and modify the single child.
  return <div style={{ border: '1px solid red' }}>{child}</div>;
}

function App() {
  // This works:
  // return <SingleChildWrapper><p>Hello</p></SingleChildWrapper>;

  // This will throw an error:
  return (
    <SingleChildWrapper>
      <p>Hello</p>
      <p>World</p>
    </SingleChildWrapper>
  );
}
```

By using these utilities, you can build flexible and robust components that correctly handle any combination of children passed to them.

After mastering how to work with children, you may want to learn how to access their underlying DOM nodes or component instances. Proceed to the next section on [Refs](./core-apis-refs.md) to learn more.