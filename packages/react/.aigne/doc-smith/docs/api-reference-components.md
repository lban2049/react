# Components & Classes

This section provides a comprehensive reference for the fundamental building blocks of React applications: the base classes (`Component`, `PureComponent`) and built-in components (`Fragment`, `Suspense`, etc.). These are essential for creating both simple and complex user interfaces.

For building modern React applications with functional components, see the [Hooks](./api-reference-hooks.md) documentation.

---

## React.Component

`React.Component` is the base class for React components when they are defined using ES6 classes. It provides the core functionality for component lifecycle, state management, and event handling.

```javascript Component Example icon=logos:javascript
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

### Instance Methods

#### `setState()`

`setState()` enqueues changes to the component state and tells React that this component and its children need to be re-rendered with the updated state. This is the primary method you use to update the user interface in response to event handlers and server responses.

React may batch multiple `setState()` calls into a single update for performance. Because `this.props` and `this.state` may be updated asynchronously, you should not rely on their values for calculating the next state.

**Parameters**

<x-field data-name="partialState" data-type="object | function" data-required="true" data-desc="An object with state changes or a function that returns an object with state changes. The function receives the previous state and current props as arguments: (prevState, props) => newState."></x-field>
<x-field data-name="callback" data-type="function" data-required="false" data-desc="An optional function that will be executed once the state is updated and the component is re-rendered."></x-field>

**Examples**

Using an object:
```javascript setState with Object icon=logos:javascript
this.setState({ quantity: this.state.quantity + 1 });
```

Using a function is safer when the new state depends on the previous state:
```javascript setState with Function icon=logos:javascript
this.setState((prevState, props) => {
  return { counter: prevState.counter + props.increment };
});
```

#### `forceUpdate()`

By default, when your component's state or props change, your component will re-render. If your `render()` method depends on some other data, you can tell React that the component needs re-rendering by calling `forceUpdate()`. Calling `forceUpdate()` will cause `render()` to be called on the component, skipping `shouldComponentUpdate()`.

**Parameters**

<x-field data-name="callback" data-type="function" data-required="false" data-desc="An optional function that will be executed after the update is complete."></x-field>


### Instance Properties

<x-field data-name="props" data-type="object" data-desc="The props passed to the component. These are read-only."></x-field>
<x-field data-name="state" data-type="object" data-desc="The local state of the component. It should be an object and can be changed using setState()."></x-field>
<x-field data-name="context" data-type="object" data-desc="The context value for the component, if it's subscribed to a context."></x-field>
<x-field data-name="refs" data-type="object" data-desc="An object containing references to DOM nodes or React components."></x-field>

### Deprecated APIs

The following APIs are deprecated and will show warnings in development mode. You should avoid using them.

| API | Alternative | Reason for Deprecation |
|---|---|---|
| `isMounted()` | Check for mounted state via a property in `componentDidMount` and `componentWillUnmount`. | `isMounted()` is an anti-pattern. Ensure cleanup happens in `componentWillUnmount` to prevent memory leaks. |
| `replaceState()` | Use `setState()` instead. | `replaceState()` was removed to encourage a more predictable state update model. See [GitHub issue #3236](https://github.com/facebook/react/issues/3236) for details. |

---

## React.PureComponent

`React.PureComponent` is similar to `React.Component`. The difference between them is that `React.Component` doesn't implement `shouldComponentUpdate()`, but `React.PureComponent` implements it with a shallow prop and state comparison.

If your React component's `render()` function renders the same result given the same props and state, you can use `React.PureComponent` for a performance boost in some cases.

```javascript PureComponent Example icon=logos:javascript
class Greeting extends React.PureComponent {
  render() {
    return <h1>Hello, {this.props.name}!</h1>;
  }
}
```
`PureComponent`'s `shouldComponentUpdate()` only shallowly compares the objects. If these contain complex data structures, it may produce false-negatives for deeper differences.

---

## Built-in Components

React provides a few built-in components that you can use in your JSX.

### Fragment

`React.Fragment` lets you group a list of children without adding extra nodes to the DOM. This is useful when a component needs to return multiple elements.

```javascript React.Fragment Example icon=logos:javascript
function UserInfo() {
  return (
    <React.Fragment>
      <dt>Name</dt>
      <dd>John Doe</dd>
      <dt>Email</dt>
      <dd>john.doe@example.com</dd>
    </React.Fragment>
  );
}
```

There is also a shorter syntax `<>...</>`. However, the shorthand syntax does not accept keys or attributes.

```javascript Fragment Shorthand icon=logos:javascript
function UserInfo() {
  return (
    <>
      <dt>Name</dt>
      <dd>John Doe</dd>
    </>
  );
}
```

### Suspense

`React.Suspense` lets you display a fallback UI while your components are waiting for something to load. It's commonly used for code-splitting with `React.lazy`.

**Props**

<x-field data-name="fallback" data-type="ReactNode" data-required="true" data-desc="A React element to render while the children are suspended (e.g., a loading spinner)."></x-field>

```javascript Suspense Example icon=logos:javascript
const OtherComponent = React.lazy(() => import('./OtherComponent'));

function MyComponent() {
  return (
    <div>
      <Suspense fallback={<div>Loading...</div>}>
        <OtherComponent />
      </Suspense>
    </div>
  );
}
```

### Profiler

`React.Profiler` measures how often a React application renders and what the “cost” of rendering is. Its purpose is to help identify parts of an application that are slow and may benefit from optimizations like memoization.

**Props**

<x-field data-name="id" data-type="string" data-required="true" data-desc="A unique identifier for the Profiler to identify which part of the tree is being measured."></x-field>
<x-field data-name="onRender" data-type="function" data-required="true" data-desc="A callback function that React calls every time a component within the profiled tree commits an update."></x-field>

```javascript Profiler Example icon=logos:javascript
function onRenderCallback(
  id, // the "id" prop of the Profiler tree that has just committed
  phase, // "mount" (if the tree just mounted) or "update" (if it re-rendered)
  actualDuration, // time spent rendering the committed update
  baseDuration, // estimated time to render the entire subtree without memoization
  startTime, // when React began rendering this update
  commitTime, // when React committed this update
  interactions // the set of interactions belonging to this update
) {
  // Aggregate or log render timings...
}

function App() {
  return (
    <Profiler id="Application" onRender={onRenderCallback}>
      <MyComponent />
    </Profiler>
  );
}
```

### StrictMode

`React.StrictMode` is a tool for highlighting potential problems in an application. Like `Fragment`, `StrictMode` does not render any visible UI. It activates additional checks and warnings for its descendants. These checks only run in development mode; they do not impact the production build.

```javascript StrictMode Example icon=logos:javascript
import React from 'react';

function App() {
  return (
    <React.StrictMode>
      <div>
        <Header />
        <MainContent />
      </div>
    </React.StrictMode>
  );
}
```
