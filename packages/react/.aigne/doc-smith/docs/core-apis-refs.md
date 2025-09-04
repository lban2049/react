# Refs

Refs provide a way to access DOM nodes or React components created in the `render` method. In the typical React dataflow, props are the only way that parent components interact with their children. To modify a child, you re-render it with new props. However, there are a few cases where you need to imperatively modify a child outside of the typical dataflow. The child to be modified could be an instance of a React component, or a DOM element.

Common use cases for refs include:
- Managing focus, text selection, or media playback.
- Triggering imperative animations.
- Integrating with third-party DOM libraries.

This section covers the core APIs for creating and forwarding refs. For function components, the `useRef` hook is the modern and recommended approach. You can learn more in the [Ref Hooks](./hooks-ref.md) documentation.

## createRef

`React.createRef` creates a ref object that can be attached to a React element via the `ref` attribute. The function returns a mutable ref object whose `.current` property is initialized to `null`. When the `ref` attribute is used on an element, React assigns the DOM element or class component instance to the `.current` property of the ref object.

The implementation of `createRef` is straightforward; it returns a simple object container:

```javascript
// an immutable object with a single mutable value
export function createRef(): RefObject {
  const refObject = {
    current: null,
  };
  if (__DEV__) {
    Object.seal(refObject);
  }
  return refObject;
}
```

### Usage

Refs are commonly assigned to an instance property in a class component's constructor so they can be referenced throughout the component.

**Example: Focusing an input element**

In this example, we create a ref in the `MyInput` component's constructor, attach it to the `<input>` element in the `render` method, and use it in an event handler to focus the input programmatically.

```jsx
class MyInput extends React.Component {
  constructor(props) {
    super(props);
    // Create a ref to store the textInput DOM element
    this.textInput = React.createRef();
    this.focusTextInput = this.focusTextInput.bind(this);
  }

  focusTextInput() {
    // Explicitly focus the text input using the raw DOM API
    // Note: we're accessing "current" to get the DOM node
    this.textInput.current.focus();
  }

  render() {
    // Use the `ref` callback to store a reference to the text input DOM
    // element in an instance field (for example, this.textInput).
    return (
      <div>
        <input
          type="text"
          ref={this.textInput} />
        <input
          type="button"
          value="Focus the text input"
          onClick={this.focusTextInput}
        />
      </div>
    );
  }
}
```

## forwardRef

By default, you cannot pass a `ref` attribute to a function component. `ref` is not a prop. Much like `key`, it’s handled differently by React. If you want to allow a parent component to get a ref to a DOM node within a child component, you need to use `React.forwardRef`.

`React.forwardRef` is a higher-order component that takes a render function. This function receives `props` and `ref` as arguments and returns a React node. The `ref` can then be forwarded to an element inside the component.

### How Ref Forwarding Works

The following diagram illustrates how a ref created in a parent component is forwarded through a custom `FancyButton` component to the underlying DOM `<button>` element.

```d2
direction: down

Parent-Component: {
  label: "Parent Component"
  shape: class

  create: {
    label: "1. const buttonRef = React.createRef();"
  }

  render: {
    label: "2. <FancyButton ref={buttonRef} />"
  }

  access: {
    label: "5. Access via buttonRef.current"
  }
}

FancyButton: {
  label: "FancyButton = forwardRef((props, ref) => ...)"
  shape: rectangle

  receive: {
    label: "3. Receives forwarded ref"
  }

  attach: {
    label: "4. Attaches ref to DOM element: <button ref={ref} />"
  }
}

DOM-Button: {
  label: "DOM <button> Element"
  shape: cylinder
}

Parent-Component.render -> FancyButton.receive: "Passes ref"
FancyButton.attach -> DOM-Button: "Connects ref"
DOM-Button -> Parent-Component.access: "Populates .current"

```

### Usage

Here is an example of a `FancyButton` component that uses `forwardRef` to pass a ref to the underlying button DOM element:

```jsx
const FancyButton = React.forwardRef((props, ref) => (
  <button ref={ref} className="FancyButton">
    {props.children}
  </button>
));

// You can now get a ref to the underlying DOM button:
const ref = React.createRef();

function App() {
  // In a real application, you might use this ref to focus the button after an action.
  return <FancyButton ref={ref}>Click me!</FancyButton>;
}
```

In this example, the `App` component passes its `ref` down to the `FancyButton`. The `FancyButton` then forwards that same `ref` to the `<button>` element it renders. As a result, `ref.current` in the `App` component will point directly to the `<button>` DOM node.

## Next Steps

You now understand how to create and forward refs using React's core APIs. These are essential tools for interacting with the DOM when the declarative approach isn't suitable.

- To learn the modern way of handling refs in function components, see the [Ref Hooks](./hooks-ref.md) documentation.
- To explore another method for passing data through the component tree, check out the [Context](./core-apis-context.md) API.