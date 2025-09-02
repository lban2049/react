# Components & Props

Components are the fundamental building blocks of React applications. They are independent and reusable pieces of code that, when combined, form the user interface. This section covers how to create both function and class components, and how to pass data between them using props.

## Understanding Components

In React, a component is essentially a JavaScript function or class that returns a React element, which describes what should appear on the screen. Components let you split the UI into independent, reusable pieces, allowing you to think about each piece in isolation.

Here's a diagram illustrating the basic relationship between a parent and child component:

```d2
direction: down

ParentComponent: {
  shape: rectangle
  "State or Data: { user: 'Jane' }"
}

ChildComponent: {
  shape: rectangle
  "Receives props and renders UI"
}

ParentComponent -> ChildComponent: "Passes data via props (e.g., name={user})"
```

### Function Components

Function components are the modern and recommended way to define components in React. They are simple JavaScript functions that accept a single `props` object argument with data and return a React element.

```javascript
// A simple function component
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

// Usage
const element = <Welcome name="Sara" />;
```

### Class Components

Class components are an alternative way to define components using ES6 classes. They extend from `React.Component` and must include a `render()` method.

As seen in the React source, the base `Component` class is initialized with `props`, `context`, and an `updater`.

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

Here is a basic example of a class component:

```javascript
import React, { Component } from 'react';

class Welcome extends Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

Class components have additional features like local state and lifecycle methods, although Hooks now provide these capabilities to function components as well.

#### `setState()`

This method is used to update a component's local state. It tells React that the component and its children need to be re-rendered with the updated state.

- **Asynchronous Nature**: Calls to `setState` are asynchronous and may be batched together for performance. Accessing `this.state` right after calling `setState` might not yield the new value.
- **Functional Updates**: You can pass a function to `setState` to ensure the update is based on the most recent state and props.

```javascript
// From src/ReactBaseClasses.js
Component.prototype.setState = function (partialState, callback) {
  // ...
  this.updater.enqueueSetState(this, partialState, callback, 'setState');
};
```

#### `forceUpdate()`

The `forceUpdate()` method causes a component to re-render, skipping `shouldComponentUpdate()`. This is rarely necessary and should be avoided. It's typically used when state deep within a component changes without a `setState` call.

```javascript
// From src/ReactBaseClasses.js
Component.prototype.forceUpdate = function (callback) {
  this.updater.enqueueForceUpdate(this, callback, 'forceUpdate');
};
```

### `PureComponent`

`React.PureComponent` is similar to `React.Component` but implements `shouldComponentUpdate()` with a shallow comparison of props and state. This can be a simple way to optimize the performance of certain components by preventing unnecessary re-renders.

```javascript
// From src/ReactBaseClasses.js
function PureComponent(props, context, updater) {
  // ... initialization is similar to Component
}

// ...
pureComponentPrototype.isPureReactComponent = true;
```

## Props (Properties)

Props are arguments passed into React components. They are the primary way to pass data from a parent component to a child component. A key principle in React is that **props are read-only**. A component must never modify its own props.

### Passing and Accessing Props

You can pass any JavaScript value as a prop, including strings, numbers, arrays, objects, and functions.

```javascript
function UserProfile(props) {
  return (
    <div>
      <h2>{props.user.name}</h2>
      <p>Age: {props.user.age}</p>
      <button onClick={props.onFollow}>Follow</button>
    </div>
  );
}

function App() {
  const userData = { name: 'John Doe', age: 30 };
  const handleFollow = () => console.log('Follow button clicked!');

  return <UserProfile user={userData} onFollow={handleFollow} />;
}
```

In this example, the `App` component passes an object (`userData`) and a function (`handleFollow`) as props to the `UserProfile` component.

---

Understanding components and props is crucial for building any React application. With this foundation, you can start composing complex UIs from simple, isolated pieces. To learn how to write the HTML-like syntax used in these examples, proceed to the next section on [JSX](./core-apis-jsx.md).
