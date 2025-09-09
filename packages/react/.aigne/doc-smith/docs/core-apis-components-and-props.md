# Components & Props

Components are the core building blocks of any React application. They let you split the UI into independent, reusable pieces, and think about each piece in isolation. Conceptually, components are like JavaScript functions. They accept arbitrary inputs (called “props”) and return React elements describing what should appear on the screen.

### Function and Class Components

The simplest way to define a component is to write a JavaScript function:

```javascript Function Component Example icon=logos:javascript
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```

This is a **Function Component**. It accepts a single “props” (which stands for properties) object argument with data and returns a React element. We recommend using Function Components for all new code.

You can also use an ES6 class to define a component:

```javascript Class Component Example icon=logos:javascript
import React, { Component } from 'react';

class Welcome extends Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

This is a **Class Component**. It extends `React.Component` and must implement a `render()` method, which returns a React element. Data is accessed via `this.props`.

### The `Component` Base Class

When you define a class component, it inherits from the base `React.Component` class. This base class provides the essential functionality for a component to manage its state and lifecycle.

Key features provided by `React.Component` include:

*   **`this.props`**: Contains the properties passed to the component from its parent.
*   **`this.context`**: Holds the context value from the nearest Context provider.
*   **`this.refs`**: An object to hold references to DOM nodes or other React components.
*   **`this.updater`**: An object containing methods to queue state updates.

The base class also defines two primary methods for requesting a re-render:

1.  **`setState(partialState, callback)`**: This is the primary method you use to update a component's local state. It merges the `partialState` into the current state and triggers a re-render. React may batch multiple `setState()` calls for performance, so the update is not guaranteed to be synchronous.

    ```javascript setState Method icon=logos:javascript
    // From React's base Component class
    Component.prototype.setState = function (partialState, callback) {
      // ... validation logic ...
      this.updater.enqueueSetState(this, partialState, callback, 'setState');
    };
    ```

2.  **`forceUpdate(callback)`**: This method forces a component to re-render, skipping the `shouldComponentUpdate()` check. It should be used sparingly, typically when you know some deep aspect of the component's state has changed without a `setState()` call.

    ```javascript forceUpdate Method icon=logos:javascript
    // From React's base Component class
    Component.prototype.forceUpdate = function (callback) {
      this.updater.enqueueForceUpdate(this, callback, 'forceUpdate');
    };
    ```

### `PureComponent`

`React.PureComponent` is a specialized version of `React.Component`. The only difference is that it implements a shallow comparison of props and state in its `shouldComponentUpdate()` lifecycle method. This can provide a performance boost in certain situations by preventing unnecessary re-renders when the props and state have not changed.

```javascript PureComponent Definition icon=logos:javascript
// PureComponent inherits from a dummy constructor that shares Component's prototype
const pureComponentPrototype = (PureComponent.prototype = new ComponentDummy());
pureComponentPrototype.constructor = PureComponent;
// Avoid an extra prototype jump for these methods.
assign(pureComponentPrototype, Component.prototype);
pureComponentPrototype.isPureReactComponent = true;
```

Use `PureComponent` when your component's render output depends solely on its props and state, and both are simple data structures.

### Composing Components

Components can refer to other components in their output. This lets us use the same component abstraction for any level of detail. A button, a form, a dialog, a screen: in React apps, all of those are commonly expressed as components.

For example, we can create an `App` component that renders `Welcome` many times:

```javascript Composing Components icon=logos:javascript
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

function App() {
  return (
    <div>
      <Welcome name="Sara" />
      <Welcome name="Cahal" />
      <Welcome name="Edite" />
    </div>
  );
}
```

### Props are Read-Only

Whether you declare a component as a function or a class, it must never modify its own props. Consider this `sum` function:

```javascript
function sum(a, b) {
  return a + b;
}
```

Such functions are called “pure” because they do not attempt to change their inputs, and always return the same result for the same inputs. In contrast, this function is impure because it changes its own input:

```javascript
function withdraw(account, amount) {
  account.total -= amount; // Don't do this!
}
```

React is flexible but it has a single strict rule:

**All React components must act like pure functions with respect to their props.**

Of course, application UIs are dynamic and change over time. In the next sections, you will learn about "state", which allows React components to change their output in response to user actions, network responses, and anything else, without violating this rule.

---

Now that you understand the basics of components and props, the next step is to learn the syntax used to define them. Continue to the [JSX](./core-apis-jsx.md) section to learn more.