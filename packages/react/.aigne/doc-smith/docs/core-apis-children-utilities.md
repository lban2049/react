# Children Utilities

In React, `props.children` is a special prop that allows components to be composed. While you can pass anything as children—from a single JSX element to an array of elements, or even a function—the actual data structure is opaque. You should not rely on it being a specific type like an array, as its implementation can change.

To work with `props.children` in a safe and predictable way, React provides the `React.Children` utility object. These helpers allow you to iterate, map, count, and manipulate the children prop without making unsafe assumptions.

This is particularly useful when designing reusable components like lists, layouts, or wrappers that need to interact with the elements passed to them.

## `React.Children.map()`

Invokes a function on every immediate child contained within `children`, returning a new array with the results. If `children` is `null` or `undefined`, this method returns `null` or `undefined` instead of an array.

### Syntax

```javascript
React.Children.map(children, function(child, index) { /* ... */ }, [context])
```

### Parameters

| Name      | Type       | Description                                                                 |
|-----------|------------|-----------------------------------------------------------------------------|
| `children`| `ReactNodeList` | The `props.children` object to iterate over.                                |
| `function`| `Function` | The function to execute for each child. It receives the `child` and its `index`. |
| `context` | `any`      | Optional. The `this` context for the function.                              |

### Returns

An array containing the return value of the callback function for each child.

### Example

This is useful for modifying the props of child elements. The following example clones each child and adds a new CSS class.

```javascript React Component icon=logos:react
function RowList({ children }) {
  return (
    <div className="row-list">
      {React.Children.map(children, child =>
        React.cloneElement(child, {
          className: `${child.props.className || ''} list-item`
        })
      )}
    </div>
  );
}

// Usage
<RowList>
  <p>Item 1</p>
  <p className="special">Item 2</p>
  <div>Item 3</div>
</RowList>
```

## `React.Children.forEach()`

Similar to `React.Children.map()`, but it does not return an array. It is used for iterating over children without transforming them.

### Syntax

```javascript
React.Children.forEach(children, function(child, index) { /* ... */ }, [context])
```

### Parameters

| Name      | Type       | Description                                                                 |
|-----------|------------|-----------------------------------------------------------------------------|
| `children`| `ReactNodeList` | The `props.children` object to iterate over.                                |
| `function`| `Function` | The function to execute for each child. It receives the `child` and its `index`. |
| `context` | `any`      | Optional. The `this` context for the function.                              |

### Example

```javascript React Component icon=logos:react
function ChildLogger({ children }) {
  React.Children.forEach(children, (child, index) => {
    console.log(`Child at index ${index} is:`, child);
  });

  return <div>{children}</div>;
}
```

## `React.Children.count()`

Returns the total number of components in `children`, which is equal to the number of times a callback passed to `map` or `forEach` would be invoked.

### Syntax

```javascript
React.Children.count(children)
```

### Parameters

| Name      | Type       | Description                               |
|-----------|------------|-------------------------------------------|
| `children`| `ReactNodeList` | The `props.children` object to count.     |

### Example

```javascript React Component icon=logos:react
function ItemCounter({ children }) {
  const count = React.Children.count(children);
  return <div>There are {count} items.</div>;
}

// Usage
<ItemCounter>
  <span>Apple</span>
  <span>Banana</span>
  <span>Orange</span>
</ItemCounter>
// Renders: <div>There are 3 items.</div>
```

## `React.Children.toArray()`

Returns the `children` opaque data structure as a flat array with keys assigned to each child element. This is useful if you want to manipulate child collections in your render methods, especially if you want to reorder or slice `props.children`.

### Example

This example takes a set of children and renders them in reverse order.

```javascript React Component icon=logos:react
function ReverseOrder({ children }) {
  const childArray = React.Children.toArray(children);
  
  return (
    <div>
      {childArray.reverse()}
    </div>
  );
}

// Usage
<ReverseOrder>
  <p key="A">First</p>
  <p key="B">Second</p>
  <p key="C">Third</p>
</ReverseOrder>
// Renders "Third", then "Second", then "First"
```

## `React.Children.only()`

Verifies that `children` has only one child (a React element) and returns it. If not, this method will throw an error. It's useful for creating components that must act as a wrapper around a single element.

### Syntax

```javascript
React.Children.only(children)
```

### Example

```javascript React Component icon=logos:react
function SingleChildWrapper({ children }) {
  // This will throw an error if children is not a single React element.
  const singleChild = React.Children.only(children);

  // Now you can safely work with singleChild
  return (
    <div style={{ border: '1px solid red' }}>
      {singleChild}
    </div>
  );
}

// Valid usage:
<SingleChildWrapper>
  <p>Hello world</p>
</SingleChildWrapper>

// Invalid usage (will throw error):
<SingleChildWrapper>
  <p>One</p>
  <p>Two</p>
</SingleChildWrapper>
```

---

By using these utilities, you can build robust and flexible components that correctly handle any type of children passed to them. 

Next, you can learn how to access DOM nodes or React components directly using [Refs](./core-apis-refs.md).