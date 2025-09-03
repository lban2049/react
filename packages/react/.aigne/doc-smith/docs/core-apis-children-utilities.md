# Children Utilities

In React, `props.children` allows components to be composed, but it's an opaque data structure. It can be a single JSX element, an array of elements, a string, a number, or even `undefined`. Directly manipulating `props.children` with standard JavaScript methods like `map` can lead to errors if the value isn't an array.

The `React.Children` object provides a set of utility functions specifically designed to work with this opaque structure safely and efficiently. These helpers correctly handle any type of children you pass, ensuring your components are robust and predictable.

```d2
direction: down

"props.children": {
  shape: package
  label: "Opaque data structure"
  grid-columns: 2

  "Single Element": { shape: rectangle }
  "Array of Elements": { shape: rectangle }
  "String or Number": { shape: rectangle }
  "null or undefined": { shape: rectangle }
}

"React.Children Utilities": {
  shape: rectangle
  style: {
    stroke: "#0052cc"
  }
  grid-columns: 3

  "map": { shape: class }
  "forEach": { shape: class }
  "count": { shape: class }
  "toArray": { shape: class }
  "only": { shape: class }
}

"Transformed Children": {
  shape: package
  label: " predictable output"
  style.fill: "#f6ffed"

  "Cloned elements with stable keys": { shape: rectangle }
}

"props.children" -> "React.Children Utilities": "Process with"
"React.Children Utilities" -> "Transformed Children": "Returns"
```

---

## `React.Children.map`

Invokes a function on every immediate child contained within `children`, returning a new array of the results. It is similar to `Array.prototype.map()`, but it handles cases where `children` is a single element or `null` without throwing an error.

React automatically assigns a new, stable key to each returned element, which is crucial for preserving state and optimizing performance during updates. The keys are constructed based on the original child's key and its position in the structure.

**Syntax**
```javascript
React.Children.map(children, function(child, index))
```

**Example**

This `List` component wraps each of its children in a `<li>` element.

```jsx
import { Children } from 'react';

function List({ children }) {
  return (
    <ul>
      {Children.map(children, (child, index) => (
        <li key={index}>{child}</li>
      ))}
    </ul>
  );
}

function App() {
  return (
    <List>
      <span>First Item</span>
      <span>Second Item</span>
      <span>Third Item</span>
    </List>
  );
}
```

## `React.Children.forEach`

Like `React.Children.map()`, but does not return an array. It is useful for iterating over the children collection without creating a new one.

**Syntax**
```javascript
React.Children.forEach(children, function(child, index))
```

**Example**

This component iterates over its children and logs their type to the console.

```jsx
import { Children } from 'react';

function ChildLogger({ children }) {
  Children.forEach(children, (child, index) => {
    console.log(`Child at index ${index} is of type:`, child.type);
  });

  return <div>{children}</div>;
}

function App() {
  return (
    <ChildLogger>
      <h1>Title</h1>
      <p>Paragraph</p>
    </ChildLogger>
  );
}
```

## `React.Children.count`

Returns the total number of components in `children`, equal to the number of times that a callback passed to `map` or `forEach` would be invoked.

**Syntax**
```javascript
React.Children.count(children)
```

**Example**

```jsx
import { Children } from 'react';

function ItemCounter({ children }) {
  const count = Children.count(children);
  return <div>This component has {count} children.</div>;
}

function App() {
  return (
    <ItemCounter>
      <p>Item 1</p>
      <p>Item 2</p>
    </ItemCounter>
  );
  // Renders: <div>This component has 2 children.</div>
}
```

## `React.Children.toArray`

Returns the `children` opaque data structure as a flattened array with keys assigned to each child. This is useful if you want to manipulate the collection of children in your render methods, especially if you want to reorder or slice `props.children`.

**Syntax**
```javascript
React.Children.toArray(children)
```

**Example**

This component takes its children, converts them to an array, and renders them in reverse order.

```jsx
import { Children } from 'react';

function ReverseOrder({ children }) {
  const childArray = Children.toArray(children);
  return <div>{childArray.reverse()}</div>;
}

function App() {
  return (
    <ReverseOrder>
      <span>One</span>
      <span>Two</span>
      <span>Three</span>
    </ReverseOrder>
  );
  // Renders the spans in the order: Three, Two, One
}
```

## `React.Children.only`

Verifies that `children` has only one child (a React element) and returns it. If `children` is not a single React element, this function will throw an error. It does not accept an array with a single element; the child must be passed directly.

**Syntax**
```javascript
React.Children.only(children)
```

**Example**

This component ensures it only ever receives a single child element.

```jsx
import { Children } from 'react';

function SingleChildWrapper({ children }) {
  // This will throw an error if more than one child is passed.
  const singleChild = Children.only(children);
  
  // You can now safely clone or inspect the single child.
  return <div style={{ border: '1px solid red' }}>{singleChild}</div>;
}

function App() {
  return (
    <SingleChildWrapper>
      <p>This is the only allowed child.</p>
    </SingleChildWrapper>
  );
}
```

---

By using these utilities, you can build flexible and powerful components that correctly handle any children passed to them. To learn more about how components receive data, see the guide on [Components & Props](./core-apis-components-and-props.md).