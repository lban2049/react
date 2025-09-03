# Code Splitting with `lazy` and `Suspense`

Code-splitting is a technique used to improve the performance of a web application by splitting a large bundle of code into smaller chunks. These chunks can then be loaded on demand, reducing the initial load time and improving the user experience. React provides two core features, `React.lazy` and `Suspense`, to make code-splitting straightforward and declarative.

This guide will walk you through how to use `lazy` and `Suspense` to load components only when they are needed.

## The `React.lazy` Function

`React.lazy` enables you to render a dynamically imported component as a regular component. It takes a function as its argument that must call a dynamic `import()`.

### How to Use It

The `lazy` function returns a special `LazyComponent` object. This object has a specific structure that React understands, with a `$$typeof` of `REACT_LAZY_TYPE`.

Before code-splitting:
```javascript
import MyComponent from './MyComponent';

const App = () => (
  <div>
    <MyComponent />
  </div>
);
```

After code-splitting with `React.lazy`:
```javascript
import React, { Suspense } from 'react';

const MyComponent = React.lazy(() => import('./MyComponent'));

const App = () => (
  <div>
    <Suspense fallback={<div>Loading...</div>}>
      <MyComponent />
    </Suspense>
  </div>
);
```

The `import()` call returns a `Promise` (specifically, a `Thenable`). This promise resolves to a module object that must have a `default` export containing the React component. If the module doesn't have a `default` export, the import will fail.

### Internal State Machine

A `LazyComponent`'s payload internally moves through several states to manage the asynchronous loading process. This mechanism is what allows `Suspense` to show a fallback UI.

```d2
direction: down

"Initial Render": {
  shape: oval
}

"Uninitialized": {
  shape: rectangle
  label: "Uninitialized (-1)"
}

"Pending": {
  shape: rectangle
  label: "Pending (0)"
  "The dynamic import() promise is in flight."
}

"Resolved": {
  shape: rectangle
  label: "Resolved (1)"
  style.fill: "#f6ffed"
  "The component module has loaded successfully."
}

"Rejected": {
  shape: rectangle
  label: "Rejected (2)"
  style.fill: "#fff1f0"
  "The component module failed to load."
}

"Component Renders": {
  shape: oval
}

"Error Boundary": {
  shape: oval
}

"Initial Render" -> "Uninitialized": "lazy() creates the component"
"Uninitialized" -> "Pending": "First render triggers lazyInitializer()"
"Pending" -> "Resolved": "import() promise resolves"
"Pending" -> "Rejected": "import() promise rejects"
"Resolved" -> "Component Renders": "React renders the component's default export"
"Rejected" -> "Error Boundary": "React throws the error"

```

## The `Suspense` Component

A component created with `React.lazy` can only be rendered inside a `Suspense` component. `Suspense` allows you to specify a loading indicator (the `fallback` prop) that is displayed while the lazy component's code is being fetched and loaded.

```javascript
import React, { Suspense } from 'react';

const OtherComponent = React.lazy(() => import('./OtherComponent'));

function MyComponent() {
  return (
    <div>
      <Suspense fallback={<div>Loading component...</div>}>
        <OtherComponent />
      </Suspense>
    </div>
  );
}
```

The `fallback` prop accepts any React elements that you want to render while waiting for the component to load. You can place the `Suspense` component anywhere above the lazy component, allowing you to create a shared loading state for multiple lazy components.

## Handling Errors

If the dynamic `import()` fails (for example, due to a network error), it will trigger an error. To handle these errors gracefully and display a user-friendly message, you can wrap your lazy component (and its `Suspense` boundary) in a standard React [Error Boundary](https://react.dev/reference/react/Component#catching-rendering-errors-with-an-error-boundary).

Here is an example of an `ErrorBoundary` component:

```javascript
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    // Update state so the next render will show the fallback UI.
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    // You can also log the error to an error reporting service
    console.error("Uncaught error:", error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      // You can render any custom fallback UI
      return <h1>Something went wrong.</h1>;
    }

    return this.props.children; 
  }
}
```

You can then use it to wrap your lazy-loaded components:

```javascript
import React, { Suspense } from 'react';
import ErrorBoundary from './ErrorBoundary';

const MyLazyComponent = React.lazy(() => import('./MyLazyComponent'));

const App = () => {
  return (
    <div>
      <h1>My Application</h1>
      <ErrorBoundary>
        <Suspense fallback={<div>Loading...</div>}>
          <MyLazyComponent />
        </Suspense>
      </ErrorBoundary>
    </div>
  );
};
```

## Availability in Different Environments

The `lazy` function is a core part of React and is exported for both client-side and server-side environments. This ensures that code-splitting with `lazy` and `Suspense` works seamlessly across different rendering strategies, including Server-Side Rendering (SSR).

---

By leveraging `React.lazy` and `Suspense`, you can significantly improve your application's initial load performance. This pattern allows you to defer loading less critical component code until it is actually needed by the user.

For more advanced performance patterns, you might want to explore [Caching](./advanced-caching.md) and [Transitions](./advanced-transitions.md).