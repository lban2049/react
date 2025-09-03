# Refs

Refs provide a way to access DOM nodes or React elements created in the render method. They are useful when you need to manage focus, trigger animations, or integrate with third-party DOM libraries—actions that fall outside the typical data flow of React.

While powerful, refs should be used sparingly as they can make code harder to understand by breaking the top-down data flow. Before using a ref, consider if the desired behavior can be achieved with state and props.

This guide covers the core APIs for refs, `createRef` and `forwardRef`. For modern function components, the `useRef` Hook is the recommended approach. See the [Ref Hooks](./hooks-ref.md) guide for more details.

## createRef

The `createRef` function creates a ref object. Its primary feature is a `.current` property which React will populate with the corresponding DOM element or class component instance.

`createRef` is most commonly used in class components.

### Syntax

```javascript
const refObject = React.createRef();
```

The function returns a ref object containing a single mutable property: `current`. Initially, `refObject.current` is `null`. When the ref is attached to an element in the `render` method, React will assign the DOM element to the `current` property.

### Example: Accessing a DOM Node

Here is a typical use case: automatically focusing an input field when a component mounts.

```javascript
import React, { Component, createRef } from 'react';

class MyInput extends Component {
  constructor(props) {
    super(props);
    // 1. Create a ref to store the textInput DOM element
    this.textInput = createRef();
  }

  componentDidMount() {
    // 3. Access the DOM node via the .current property and call focus()
    this.textInput.current.focus();
  }

  render() {
    // 2. Attach the ref to the <input> element
    return <input type="text" ref={this.textInput} />;
  }
}
```

In this example:
1.  We create a ref named `this.textInput` inside the constructor using `createRef()`.
2.  We attach this ref to the `<input>` element in the `render` method by passing it to the `ref` attribute.
3.  Once the component mounts, React updates the `current` property of `this.textInput`. In `componentDidMount`, we can then access the input's DOM node directly and call its `focus()` method.

## forwardRef

By default, you cannot pass the `ref` prop to a function component because they don't have instances. Ref forwarding is a feature that lets components receive a ref and pass it down to a child component.

This is particularly useful for creating reusable components like styled buttons or input fields where the parent component might need direct access to the underlying DOM node.

### Syntax

```javascript
const MyComponent = React.forwardRef((props, ref) => {
  // render logic using props and ref
});
```

`React.forwardRef` accepts a render function that receives `props` and `ref` as arguments. You can then "forward" the received `ref` to an element inside the component.

### Example: Forwarding a Ref to a DOM Element

Let's create a `FancyButton` component that forwards any ref it receives to the underlying DOM `<button>` element.

```javascript
import React, { createRef, forwardRef } from 'react';

// 1. Create a component using forwardRef
const FancyButton = forwardRef((props, ref) => (
  <button ref={ref} className="FancyButton">
    {props.children}
  </button>
));

function App() {
  // 2. Create a ref to be attached to the DOM button
  const buttonRef = createRef();

  const handleClick = () => {
    // 4. Access the button's DOM node to focus it
    buttonRef.current.focus();
  };

  return (
    <>
      {/* 3. Pass the ref down to FancyButton */}
      <FancyButton ref={buttonRef}>Click me!</FancyButton>
      <button onClick={handleClick}>Focus Fancy Button</button>
    </>
  );
}
```

Here's the flow:
1.  We wrap our `FancyButton` component in `forwardRef`.
2.  `forwardRef` provides the `ref` passed by the parent as the second argument to our render function.
3.  We pass, or "forward," this `ref` down to the `<button>` element.
4.  This allows the parent `App` component to create a `buttonRef`, pass it to `<FancyButton>`, and get direct access to the underlying `<button>` DOM node.

### Ref Forwarding Flow

The following diagram illustrates how a ref is passed from a parent component, through a forwarding component, and attached to a DOM node.

```d2
direction: down

"Parent Component": {
  shape: rectangle
  "const myRef = createRef()"

  "render()": {
    "<FancyButton ref={myRef} />"
  }
}

"FancyButton = forwardRef((props, ref) => ...)": {
  shape: package
  "render()": {
    "<button ref={ref} />"
  }
}

"DOM": {
  shape: cylinder
  "<button>"
}

"Parent Component" -> "FancyButton = forwardRef((props, ref) => ...)": "1. Passes ref"
"FancyButton = forwardRef((props, ref) => ...)" -> "DOM": "2. Forwards ref to <button>"
"Parent Component" -> "DOM": "3. myRef.current now points to the <button> node" {
  style.stroke-dash: 4
}
```

---

This covers the fundamental APIs for creating and forwarding refs. While essential for certain use cases, especially in class components and component libraries, modern React applications often rely on hooks.

To learn about the modern approach, continue to the [Ref Hooks](./hooks-ref.md) guide.