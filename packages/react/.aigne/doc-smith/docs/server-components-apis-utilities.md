# Server Utilities

React Server Components leverage a set of utility functions from the core React library. These utilities provide fundamental capabilities for manipulating React elements and managing component behavior within the server environment.

For detailed information on React APIs and Hooks for server-side component development, refer to [Server Hooks](./server-components-apis-hooks.md).

## Children

The `React.Children` object provides utilities for working with the `props.children` opaque data structure. These functions help you safely iterate, transform, and count children passed to components, whether they are a single element, an array of elements, or even complex nested structures.

### `React.Children.map`

This function iterates over each immediate child in `children` and calls a function `func` on each child. If `children` is an array, `func` will be called for each child in the array. If `children` is `null` or `undefined`, this method returns `null` or `undefined` respectively.

**Parameters**

| Name | Type | Description |
|---|---|---|
| `children` | `?ReactNodeList` | The children to iterate over, typically `props.children`. Can be a single node, an array, or an iterable. |
| `func` | `(child: ?React$Node, index: number) => ?ReactNodeList` | The function to call on each child. It receives the `child` and its `index` as arguments. |
| `context` | `mixed` | The `this` context for `func`. |

**Returns**

| Name | Type | Description |
|---|---|---|
| `result` | `?Array<React$Node>` | An array containing the results of applying `func` to each child. Returns `null` or `undefined` if `children` was `null` or `undefined`. |

**Example**

```javascript
import { Children } from 'react';

function ListItemRenderer({ children }) {
  return (
    <ul>
      {Children.map(children, (child, index) => (
        <li key={index}>
          {child}
        </li>
      ))}
    </ul>
  );
}

// Example usage in a Server Component
function MyServerComponent() {
  return (
    <ListItemRenderer>
      <span>Item 1</span>
      <span>Item 2</span>
      Text Node 3
    </ListItemRenderer>
  );
}
```

This example wraps each child of `ListItemRenderer` in a `<li>` element, demonstrating how to transform children.

### `React.Children.forEach`

Similar to `map`, but it does not return a new array. It iterates over each immediate child and calls `forEachFunc` on each one. This is useful when you need to perform an action on each child without modifying them.

**Parameters**

| Name | Type | Description |
|---|---|---|
| `children` | `?ReactNodeList` | The children to iterate over. |
| `forEachFunc` | `(child: ?React$Node, index: number) => void` | The function to call for each child. It receives the `child` and its `index`. |
| `forEachContext` | `mixed` | The `this` context for `forEachFunc`. |

**Example**

```javascript
import { Children } from 'react';

function LogChildren({ children }) {
  Children.forEach(children, (child, index) => {
    console.log(`Child at index ${index}:`, child);
  });
  return null; // Or return children directly
}

// Example usage
function App() {
  return (
    <LogChildren>
      <p>Hello</p>
      <span>World</span>
    </LogChildren>
  );
}
```

This example iterates through children and logs each one to the console.

### `React.Children.count`

Returns the total number of components in `children`, which is equal to the number of times the callback passed to `map` or `forEach` would be invoked.

**Parameters**

| Name | Type | Description |
|---|---|---|
| `children` | `?ReactNodeList` | The children to count. |

**Returns**

| Name | Type | Description |
|---|---|---|
| `count` | `number` | The total number of children. |

**Example**

```javascript
import { Children } from 'react';

function ChildrenCounter({ children }) {
  const count = Children.count(children);
  return <p>This component has {count} children.</p>;
}

// Example usage
function App() {
  return (
    <ChildrenCounter>
      <span>One</span>
      <span>Two</span>
      <span>Three</span>
    </ChildrenCounter>
  );
}
```

This example counts the number of children and displays it.

### `React.Children.toArray`

Flattens a children object (typically specified as `props.children`) into an array. This function is particularly useful for reordering or slicing children before passing them to another component.

**Parameters**

| Name | Type | Description |
|---|---|---|
| `children` | `?ReactNodeList` | The children to flatten into an array. |

**Returns**

| Name | Type | Description |
|---|---|---|
| `array` | `Array<React$Node>` | An array containing all the children. |

**Example**

```javascript
import { Children } from 'react';

function ReverseChildren({ children }) {
  const childrenArray = Children.toArray(children);
  const reversedChildren = childrenArray.slice().reverse();
  return (
    <div>
      {reversedChildren}
    </div>
  );
}

// Example usage
function App() {
  return (
    <ReverseChildren>
      <div>First</div>
      <div>Second</div>
      <div>Third</div>
    </ReverseChildren>
  );
}
```

This example converts children to an array, reverses their order, and then renders them.

### `React.Children.only`

Verifies that `children` has only one child (a React element) and returns it. Otherwise, this method throws an error.

**Parameters**

| Name | Type | Description |
|---|---|---|
| `children` | `T` | The children object to check. |

**Returns**

| Name | Type | Description |
|---|---|---|
| `child` | `T` | The single React element child. |

**Example**

```javascript
import { Children } from 'react';

function SingleChildWrapper({ children }) {
  const singleChild = Children.only(children);
  return (
    <div style={{ border: '1px solid blue' }}>
      {singleChild}
    </div>
  );
}

// Example usage (will work)
function App() {
  return (
    <SingleChildWrapper>
      <span>I am the only child.</span>
    </SingleChildWrapper>
  );
}

// Example usage (will throw an error if uncommented)
/*
function AppWithError() {
  return (
    <SingleChildWrapper>
      <span>Child 1</span>
      <span>Child 2</span>
    </SingleChildWrapper>
  );
}
*/
```

This example shows how `only` ensures that a component receives exactly one child.

## createElement

The `React.createElement` function creates and returns a new React element of the given `type`. This is the core function behind JSX, which is syntactic sugar for `createElement` calls. When you write JSX, it gets transpiled into `React.createElement` calls.

**Parameters**

| Name | Type | Description |
|---|---|---|
| `type` | `any` | The type of the element. This can be a string (for DOM elements like `'div'`, `'span'`), a React component class, or a function component. |
| `config` | `object` | An object containing the element's props and `key`/`ref` (if present). These will be passed to the component. |
| `children` | `...any` | The rest of the arguments are considered children of the element. They can be elements, strings, numbers, or `null`. |

**Returns**

| Name | Type | Description |
|---|---|---|
| `element` | `ReactElement` | A new React element object. |

**Example**

```javascript
import { createElement } from 'react';

function MyButton(props) {
  return createElement('button', { className: 'my-btn', onClick: props.onClick }, props.children);
}

// Using createElement to create a div with a custom button and text
function App() {
  return createElement(
    'div',
    { style: { padding: '20px', border: '1px solid gray' } },
    createElement(MyButton, { onClick: () => console.log('Button clicked!') }, 'Click Me'),
    'Hello, React!',
  );
}
```

This example demonstrates creating elements programmatically using `createElement`, which is what JSX compiles to under the hood.

## cloneElement

The `React.cloneElement` function creates and returns a new React element using an `element` as the starting point. The new element will have the same `type` and `key` as the original element, but its `props` will be a shallow merge of the original element's `props` with new `config` and `children`. This is useful for modifying elements created by other components without directly mutating them.

**Parameters**

| Name | Type | Description |
|---|---|---|
| `element` | `ReactElement` | The existing React element to clone. |
| `config` | `object` | An object containing new props to merge with the original element's props. Can also contain a new `key` or `ref`. |
| `children` | `...any` | New children to replace the original element's children. If provided, they override the original children. |

**Returns**

| Name | Type | Description |
|---|---|---|
| `clonedElement` | `ReactElement` | A new React element, cloned and modified from the original. |

**Example**

```javascript
import { createElement, cloneElement } from 'react';

function Item({ id, children }) {
  return createElement('div', null, `Item ${id}: `, children);
}

function Wrapper({ children }) {
  const enhancedChildren = cloneElement(children, {
    id: 'Enhanced', // Override or add a prop
    style: { color: 'blue' }
  }, ' (Cloned and enhanced)'); // Replace existing children with new ones

  return createElement('div', null, enhancedChildren);
}

// Example usage
function App() {
  return createElement(Wrapper, null, createElement(Item, { id: 'Original' }, 'Content'));
}
```

This example demonstrates how `cloneElement` can be used to add new props or change children of an existing element without modifying the original.

## isValidElement

The `React.isValidElement` function verifies if an object is a React element. A React element is a plain JavaScript object that describes a component instance or DOM node and its desired properties.

**Parameters**

| Name | Type | Description |
|---|---|---|
| `object` | `any` | The object to check. |

**Returns**

| Name | Type | Description |
|---|---|---|
| `boolean` | `boolean` | `true` if the `object` is a React element, `false` otherwise. |

**Example**

```javascript
import { createElement, isValidElement } from 'react';

const myElement = createElement('div', null, 'Hello');
const myObject = { name: 'test' };

console.log(isValidElement(myElement)); // true
console.log(isValidElement(myObject));  // false
console.log(isValidElement(null));      // false
console.log(isValidElement('string'));  // false
```

This example illustrates how to use `isValidElement` to determine if a given variable holds a valid React element.

## createRef

The `React.createRef` function is a core React utility that creates a ref object. Ref objects are mutable containers with a single `current` property that can hold a reference to a DOM element or a component instance. While commonly associated with client-side DOM manipulation, `createRef` can be used in isomorphic code or for internal component logic where a mutable reference is needed, though its direct application in pure Server Components is less common since they don't interact with the DOM directly.

**Returns**

| Name | Type | Description |
|---|---|---|
| `refObject` | `RefObject` | An object with a single `current` property initialized to `null`. |

**Example**

```javascript
import { createRef } from 'react';

function MyComponent() {
  const myRef = createRef(); // Create a ref object

  // In a client-side component, you might attach it to a DOM element:
  // <input ref={myRef} />

  // In a server component context, `myRef.current` will typically remain null
  // as there's no DOM to attach to. However, the ref object itself can be passed around
  // if you're building a library that needs to handle refs in a consistent way.
  console.log(myRef.current); // null
  return null;
}
```

This example shows how to create a ref object. In a Server Component, `myRef.current` would remain `null` as there is no browser DOM.

## lazy

The `React.lazy` function lets you defer loading of a component’s code until it’s rendered for the first time. This feature is primarily used for code-splitting in client-side applications, allowing you to load components only when they are needed. While `lazy` itself is available in Server Components, its practical use for code-splitting is typically client-side, as Server Components are rendered to a static HTML or serialized representation. However, understanding its mechanism is still relevant as it can be passed down to client components or used in scenarios where a server component might reference a lazy-loaded client component.

**Parameters**

| Name | Type | Description |
|---|---|---|
| `ctor` | `() => Thenable<{default: T, ...}>` | A function that returns a `Thenable` (like a Promise) which resolves to a module object. The module object must have a `default` export containing the React component. |

**Returns**

| Name | Type | Description |
|---|---|---|
| `LazyComponent` | `LazyComponent<T, Payload<T>>` | A React component that can be rendered like a regular component. |

**Example**

```javascript
import { lazy } from 'react';

// This is typically used for client components for code-splitting
// const LazyClientComponent = lazy(() => import('./ClientComponent'));

function ServerDisplayComponent() {
  // Although `lazy` is available on the server, the actual lazy loading
  // (fetching the code) only happens on the client side when the component is rendered.
  // A Server Component would usually just pass this `LazyClientComponent` reference
  // to a Client Component to be rendered there.
  return (
    <div>
      {/* <LazyClientComponent /> is an example for client-side usage */}
      <p>React.lazy is primarily for client-side code-splitting.</p>
    </div>
  );
}
```

This example illustrates the `lazy` function's signature and its primary use case on the client, even though the API itself is exposed in the server runtime.

## memo

The `React.memo` function is a higher-order component that lets you memoize a functional component. If your component renders the same result given the same props, you can wrap it in `React.memo` for a performance boost in some cases. It prevents re-rendering the component if its props have not changed. This optimization is primarily beneficial for client-side rendering to avoid unnecessary re-renders in the browser DOM. In Server Components, where components render once to a static output, the performance benefit of `memo` is significantly reduced, though it's still available for consistency or if a component is used across both client and server boundaries.

**Parameters**

| Name | Type | Description |
|---|---|---|
| `type` | `React$ElementType` | The component type to memoize. This should be a functional component. |
| `compare` | `(oldProps: Props, newProps: Props) => boolean` | An optional comparison function. If provided, React will use it to compare the old and new props. If it returns `true`, the component will not re-render. If omitted, React performs a shallow comparison of props. |

**Returns**

| Name | Type | Description |
|---|---|---|
| `ElementType` | `ElementType` | A memoized React component. |

**Example**

```javascript
import { memo } from 'react';

function MyMemoizedComponent({ name, count }) {
  // This component will only re-render if 'name' or 'count' props change
  // (or if React.memo uses a custom 'compare' function).
  console.log('MyMemoizedComponent rendered!');
  return (
    <div>
      <p>Name: {name}</p>
      <p>Count: {count}</p>
    </div>
  );
}

const MemoizedComponent = memo(MyMemoizedComponent);

// In a Server Component, MemoizedComponent is rendered once to HTML.
// The memoization benefit (avoiding re-renders) is primarily for client-side updates.
function ServerRenderingMemo() {
  return (
    <div>
      <MemoizedComponent name="Alice" count={1} />
      <MemoizedComponent name="Bob" count={2} />
    </div>
  );
}
```

This example shows how to use `memo` to create a memoized component. While available in Server Components, its main performance impact is observed during client-side updates.

---

This section provided an overview of essential React utility functions available in the Server Components environment. These tools enable effective manipulation and management of React elements, laying the groundwork for robust application development.

To continue exploring React Server Component capabilities, proceed to learn about the [Taint Registry](./server-components-apis-taint-registry.md) to understand how sensitive data is managed in server environments.