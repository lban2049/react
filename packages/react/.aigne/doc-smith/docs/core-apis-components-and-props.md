# Components & Props

Components are the fundamental building blocks of React applications. They allow you to split the user interface into independent, reusable pieces, and think about each piece in isolation. Props, short for properties, are how components receive data from their parents to configure their appearance and behavior.

This section covers the two main ways to define components—Function and Class components—and explains how to pass data between them using props.

## Function Components

Function components are the modern and recommended way to create components in React. They are simple JavaScript functions that accept a single `props` object as an argument and return a React element to be rendered.

```javascript
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

// Usage:
const element = <Welcome name="Sara" />;
```

Initially, these were known as "stateless components." However, with the introduction of Hooks, function components can now manage their own state and perform side effects. You can learn more about this in the [Hooks](./hooks.md) section.

## Class Components

Class components are an alternative way to define components using ES6 classes. To create a class component, you extend the `React.Component` or `React.PureComponent` base classes.

```javascript
direction: down

Parent-Component: {
  shape: rectangle
  label: "Parent Component"
}

Child-Component: {
  shape: rectangle
  label: "Child Component"
}

Props: {
  shape: parallelogram
  label: "Props Data"
}

UI: {
  shape: document
  label: "Rendered UI"
}

Parent-Component -> Props: "Passes"
Props -> Child-Component: "Receives"
Child-Component -> UI: "Renders"
```

### `React.Component`

This is the primary base class for React components defined using ES6 classes. It requires you to implement a `render()` method, which returns a React element.

**Props and State**

Unlike function components that receive props as an argument, class components access props via `this.props`. They can also manage internal data using a special property called `state`.

```javascript
class Counter extends React.Component {
  constructor(props) {
    super(props);
    // State is initialized in the constructor
    this.state = { count: 0 };
  }

  // ... methods to update state ...

  render() {
    return (
      <div>
        <p>You clicked {this.state.count} times</p>
        <button onClick={() => this.increment()}>Click me</button>
      </div>
    );
  }
}
```

#### `setState(partialState, [callback])`

To modify a component's state, you must use the `setState()` method. You should treat `this.state` as immutable.

- **Asynchronous Updates**: `setState()` enqueues changes to the component's state and tells React that this component and its children need to be re-rendered with the updated state. There is no guarantee that `this.state` will be updated immediately.
- **Functional Updates**: If your next state depends on the previous state, you can pass a function to `setState`. This function will receive the previous state and props as arguments and should return an object to be merged into the state.

```javascript
// From ReactBaseClasses.js - simplified example

Component.prototype.setState = function (partialState, callback) {
  this.updater.enqueueSetState(this, partialState, callback, 'setState');
};

// Example of using setState with an object
this.setState({ count: this.state.count + 1 });

// Example of using setState with a function (safer for updates depending on previous state)
this.setState((prevState, props) => ({
  count: prevState.count + props.increment
}));
```

#### `forceUpdate([callback])`

By default, when your component’s state or props change, your component will re-render. If your `render()` method depends on some other data, you can tell React that the component needs re-rendering by calling `forceUpdate()`. This method should be used sparingly.

Calling `forceUpdate()` will cause `render()` to be called on the component, skipping `shouldComponentUpdate()`. Child components will still trigger their normal lifecycle methods.

### `React.PureComponent`

`React.PureComponent` is a variation of `React.Component`. The primary difference is that `React.Component` doesn't implement `shouldComponentUpdate()`, while `React.PureComponent` implements it with a shallow comparison of props and state.

If your component's `render()` function consistently renders the same result given the same props and state, you can use `PureComponent` for a potential performance boost in some cases.

```javascript
// From ReactBaseClasses.js

function PureComponent(props, context, updater) {
  // ... constructor logic ...
}

const pureComponentPrototype = (PureComponent.prototype = new ComponentDummy());
pureComponentPrototype.constructor = PureComponent;
assign(pureComponentPrototype, Component.prototype);
pureComponentPrototype.isPureReactComponent = true; // This flag identifies it as a PureComponent
```

## Props are Read-Only

Whether you declare a component as a function or a class, it must never modify its own props. All React components must act like pure functions with respect to their props. This ensures that the UI is predictable and easier to debug.

```javascript
// Correct: A pure function that does not change its inputs.
function sum(a, b) {
  return a + b;
}

// Incorrect: This function changes its own input.
function withdraw(account, amount) {
  account.total -= amount;
}
```

---

Now that you understand how to build and compose components, the next step is to master the syntax used to describe their UI. Proceed to the [JSX](./core-apis-jsx.md) section to learn more.