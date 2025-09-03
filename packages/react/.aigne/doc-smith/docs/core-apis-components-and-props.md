# Components & Props

Components are the core building blocks of any React application. They let you split the UI into independent, reusable pieces, and think about each piece in isolation. Conceptually, components are like JavaScript functions. They accept arbitrary inputs (called “props”) and return React elements describing what should appear on the screen.

### Function and Class Components

React offers two primary ways to define a component:

1.  **Function Components**: The simplest way to write a component. These are JavaScript functions that accept a single `props` object and return a React element.
2.  **Class Components**: These are ES6 classes that extend from `React.Component` and provide more features, such as local state and lifecycle methods. With the introduction of Hooks, you can now use these features in function components as well.

Here is an example of a simple `Welcome` component written as both a function and a class:

**Function Component**
```javascript
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```

**Class Component**
```javascript
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

Both components are equivalent from React’s point of view.

### The Component Base Class

When you define a class component, you extend `React.Component`. This base class provides the essential functionality for a component to manage its state and lifecycle.

```javascript
// From src/ReactBaseClasses.js

function Component(props, context, updater) {
  this.props = props;
  this.context = context;
  this.refs = emptyObject;
  this.updater = updater || ReactNoopUpdateQueue;
}

Component.prototype.isReactComponent = {};
```

As seen in the constructor, every class component instance has a few key properties:
*   `this.props`: Contains the properties passed to the component from its parent.
*   `this.context`: An advanced feature for passing data through the component tree without having to pass props down manually at every level. See the [Context](./core-apis-context.md) documentation for more details.
*   `this.refs`: A way to access DOM nodes or React components created in the render method. See the [Refs](./core-apis-refs.md) documentation.
*   `this.updater`: An object that handles queuing state updates and re-rendering the component.

The `isReactComponent` property is an empty object that React uses internally to differentiate class components from plain JavaScript functions.

#### Updating State

Class components have two primary methods for triggering UI updates.

1.  **`setState(partialState, callback)`**

    This is the primary method you use to update a component's local state. It tells React that the component and its children need to be re-rendered with the updated state.

    *   **Asynchronous Nature**: React may batch multiple `setState()` calls into a single update for performance. Because `this.props` and `this.state` may be updated asynchronously, you should not rely on their values for calculating the next state. Instead, use the function form of `setState`.
    *   **Merging**: When you call `setState()`, React merges the object you provide into the current state.

    ```javascript
    // Correct: Using a function to access previous state
    this.setState((prevState, props) => ({
      counter: prevState.counter + 1
    }));

    // Incorrect: May lead to bugs due to asynchronous updates
    this.setState({
      counter: this.state.counter + 1
    });
    ```

2.  **`forceUpdate(callback)`**

    By default, when your component’s state or props change, your component will re-render. If your `render()` method depends on some other data, you can tell React that the component needs re-rendering by calling `forceUpdate()`. Calling `forceUpdate()` will cause `render()` to be called on the component, skipping `shouldComponentUpdate()`. This should be used sparingly.

### PureComponent

`React.PureComponent` is similar to `React.Component` but implements the `shouldComponentUpdate()` method with a shallow prop and state comparison. If the new props and state are shallowly equal to the previous ones, the component does not re-render, providing a performance boost in certain cases.

```javascript
// From src/ReactBaseClasses.js
function PureComponent(props, context, updater) {
  this.props = props;
  this.context = context;
  this.refs = emptyObject;
  this.updater = updater || ReactNoopUpdateQueue;
}

const pureComponentPrototype = (PureComponent.prototype = new ComponentDummy());
pureComponentPrototype.constructor = PureComponent;
assign(pureComponentPrototype, Component.prototype);
pureComponentPrototype.isPureReactComponent = true;
```

### Props are Read-Only

Whether you declare a component as a function or a class, it must never modify its own props. All React components must act like pure functions with respect to their props.

*   **Correct**: A pure function that does not change its inputs.
    ```javascript
    function sum(a, b) {
      return a + b;
    }
    ```

*   **Incorrect**: An impure function that changes its own input.
    ```javascript
    function withdraw(account, amount) {
      account.total -= amount; // Don't do this!
    }
    ```

### Next Steps

Now that you understand the fundamentals of components and props, you can explore how to write them using a more concise syntax.

- Learn about [JSX](./core-apis-jsx.md) to embed XML-like syntax directly in your JavaScript code.
- Dive into the [Hooks](./hooks.md) documentation to learn how to use state and other React features in function components.