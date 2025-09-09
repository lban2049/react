# Refs

Refs provide a way to access DOM nodes or React components created in the `render` method. In the typical React dataflow, props are the only way that parent components interact with their children. To modify a child, you re-render it with new props. However, there are a few cases where you need to imperatively modify a child outside of the typical dataflow. The child to be modified could be an instance of a React component, or a DOM element.

While you can add a ref to a class component, you cannot use the `ref` attribute on function components because they don't have instances. However, you can use `forwardRef` to allow a ref to be passed through a function component to a DOM element or class component inside.

For managing refs within function components, the `useRef` Hook is the recommended approach. You can learn more about it in the [Ref Hooks](./hooks-ref.md) section.

## `createRef`

`createRef` creates a ref object that can be attached to a React element via the `ref` attribute. The function returns a mutable ref object whose `.current` property is initialized to `null`. When the `ref` attribute is used on an element, the `.current` property will be updated with the corresponding DOM node or component instance.

Internally, `createRef` is a simple function that returns an object, as seen in its implementation:

```javascript ReactCreateRef.js icon=logos:javascript
export function createRef(): RefObject {
  const refObject = {
    current: null,
  };
  // In development, the object is sealed to prevent accidental modification.
  if (__DEV__) {
    Object.seal(refObject);
  }
  return refObject;
}
```

### Usage with Class Components

Refs are commonly assigned to an instance property in the constructor, so they can be referenced throughout the component.

```javascript MyComponent.js icon=logos:react
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    // Create a ref to store the textInput DOM element
    this.textInput = React.createRef();
  }

  componentDidMount() {
    // Access the DOM node using the .current property and focus the input
    this.textInput.current.focus();
  }

  render() {
    // Attach the ref to the <input> element
    return (
      <input
        type="text"
        ref={this.textInput} />
    );
  }
}
```

In this example, React will assign the DOM element to `this.textInput.current` when the component mounts.

## `forwardRef`

Ref forwarding is a technique for automatically passing a ref through a component to one of its children. This is particularly useful for reusable component libraries and for scenarios where a parent component needs direct access to a child's DOM node.

`React.forwardRef` accepts a rendering function as its argument. React will call this function with `props` and `ref` as two arguments. This `ref` comes from the parent component and can be forwarded to an element inside the child.

```javascript ReactForwardRef.js icon=logos:javascript
export function forwardRef<Props, ElementType: React$ElementType>(
  render: (
    props: Props,
    ref: React$RefSetter<React$ElementRef<ElementType>>,
  ) => React$Node,
) {
  // ... (development checks omitted for brevity)
  const elementType = {
    $$typeof: REACT_FORWARD_REF_TYPE,
    render,
  };
  return elementType;
}
```

### Example: Forwarding a ref to a DOM element

Here is an example of a `FancyButton` function component that uses `forwardRef` to pass a `ref` it receives to the underlying DOM `<button>` element.

```javascript FancyButton.js icon=logos:react
const FancyButton = React.forwardRef((props, ref) => (
  <button ref={ref} className="FancyButton">
    {props.children}
  </button>
));

// You can now get a ref to the underlying DOM button:
const ref = React.createRef();
const App = () => (
  <FancyButton ref={ref}>Click me!</FancyButton>
);

// After rendering, ref.current will point to the <button> DOM node.
```

Here’s what happens in the example above:
1. We create a React ref by calling `React.createRef` and assign it to a `ref` variable.
2. We pass our `ref` down to `<FancyButton>` by specifying it as a JSX attribute.
3. React passes the `ref` to the `(props, ref) => ...` function inside `forwardRef` as the second argument.
4. We forward this `ref` argument down to `<button>` by specifying it as a JSX attribute.
5. When the ref is attached, `ref.current` will point to the `<button>` DOM node.

## When to Use Refs

Below are a few good use cases for refs:

*   **Managing focus, text selection, or media playback.**
*   **Triggering imperative animations.**
*   **Integrating with third-party DOM libraries.**

Avoid using refs for anything that can be done declaratively. For example, instead of exposing `open()` and `close()` methods on a `Dialog` component, pass an `isOpen` prop to it.

---

This covers the core APIs for creating and forwarding refs in class components and for passing them through function components. For a modern, hook-based approach in function components, please see the next recommended readings.

<x-cards>
  <x-card data-title="Ref Hooks" data-icon="lucide:hook" data-href="/hooks/ref">
    Learn about the `useRef` and `useImperativeHandle` Hooks for managing refs in function components.
  </x-card>
  <x-card data-title="Context" data-icon="lucide:box" data-href="/core-apis/context">
    Explore how to pass data through the component tree without having to pass props down manually.
  </x-card>
</x-cards>