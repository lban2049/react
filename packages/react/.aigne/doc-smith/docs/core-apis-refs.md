# Refs

Refs provide a way to access DOM nodes or React elements created in the render method. While you would typically let React handle all DOM manipulation, sometimes you might need to imperatively modify a child outside of the typical dataflow. Refs are the recommended way to do this.

Common use cases for refs include:
- Managing focus, text selection, or media playback.
- Triggering imperative animations.
- Integrating with third-party DOM libraries.

Avoid using refs for anything that can be done declaratively. For example, instead of exposing `open()` and `close()` methods on a `Dialog` component, pass an `isOpen` prop to it.

This section covers the core APIs for creating and forwarding refs, which are commonly used in class components. For function components, the [`useRef`](./hooks-ref.md) Hook is the modern standard.

---

## `createRef`

The `React.createRef()` function creates a ref object that can be attached to React elements via the `ref` attribute. The ref object has a mutable `current` property that holds the corresponding DOM node or component instance.

As seen in its implementation, it creates a simple object with a `current` property initialized to `null`.

```javascript
// A simplified view from src/ReactCreateRef.js
export function createRef(): RefObject {
  const refObject = {
    current: null,
  };
  return refObject;
}
```

When the `ref` attribute is used on an HTML element, the `ref`'s `current` property receives the underlying DOM element as its value once the component mounts. React will update it to `null` when the component unmounts.

### Usage in Class Components

A common pattern is to create a ref in a class component's constructor and attach it to an element in the `render` method.

```javascript
import React, { Component, createRef } from 'react';

class MyComponent extends Component {
  constructor(props) {
    super(props);
    // Create a ref to store the textInput DOM element
    this.textInput = createRef();
  }

  componentDidMount() {
    // Now that the component has mounted, we can access the DOM node
    // via the 'current' property and focus the input.
    if (this.textInput.current) {
      this.textInput.current.focus();
    }
  }

  render() {
    // Attach the ref to the <input> element. When this element is mounted
    // in the DOM, React will assign the DOM node to this.textInput.current.
    return <input type="text" ref={this.textInput} />;
  }
}
```
In this example, `this.textInput.current` will point to the `<input>` DOM node after the component mounts, allowing us to call its `focus()` method.

---

## `forwardRef`

Ref forwarding is a technique for automatically passing a ref through a component to one of its children. This is particularly useful for reusable components, as it allows the parent component to get a reference to a DOM node deep inside the child's render tree.

The `ref` attribute is not a standard prop and is handled specially by React. If you add a `ref` to a custom component, you won't get a reference to its DOM node. `React.forwardRef` solves this by creating a component that can accept a `ref` and forward it.

`forwardRef` accepts a render function that receives `props` and `ref` as arguments. You can then forward the `ref` argument to an element inside the component.

```javascript
// A simplified view from src/ReactForwardRef.js
export function forwardRef<Props, ElementType: React$ElementType>(
  render: (
    props: Props,
    ref: React$RefSetter<React$ElementRef<ElementType>>,
  ) => React$Node,
) {
  // ... internal logic ...
  const elementType = {
    $$typeof: REACT_FORWARD_REF_TYPE,
    render,
  };
  return elementType;
}
```

### Example

Let's create a `FancyButton` component that forwards any refs it receives to the underlying DOM `<button>` element.

```javascript
import React, { forwardRef, createRef } from 'react';

// Create a component that forwards the ref to the DOM button
const FancyButton = forwardRef((props, ref) => (
  <button ref={ref} className="FancyButton">
    {props.children}
  </button>
));

// Parent component that uses FancyButton
class App extends React.Component {
  constructor(props) {
    super(props);
    // Create a ref that will point to the button DOM element
    this.buttonRef = createRef();
  }

  componentDidMount() {
    // The ref now points to the <button> element rendered by FancyButton
    if (this.buttonRef.current) {
      this.buttonRef.current.style.backgroundColor = 'lightblue';
      this.buttonRef.current.focus();
    }
  }

  render() {
    return (
      <FancyButton ref={this.buttonRef}>Click me!</FancyButton>
    );
  }
}
```
Without `forwardRef`, the `ref` on `<FancyButton>` would be `null`. With it, `this.buttonRef.current` in the `App` component correctly points to the `<button>` DOM node, allowing the parent to interact with it directly.

---

### Next Steps

Refs are a necessary tool for certain imperative actions, but should be used as an escape hatch. Understanding how they work is a key part of mastering React's component model.

<x-cards>
  <x-card data-title="Ref Hooks" data-icon="lucide:hook" data-href="/hooks/ref">
    For modern React applications using function components, learn about the `useRef` and `useImperativeHandle` Hooks, which are the standard for managing refs.
  </x-card>
  <x-card data-title="Context" data-icon="lucide:box" data-href="/core-apis/context">
    Explore Context for a different way to pass data through the component tree without having to pass props down manually at every level.
  </x-card>
</x-cards>