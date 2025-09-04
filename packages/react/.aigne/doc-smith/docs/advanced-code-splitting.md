# Code Splitting with `lazy` and `Suspense`

Code splitting is a technique used to improve the performance of web applications by splitting a large bundle of code into smaller chunks that can be loaded on demand. This can significantly reduce the initial load time of your application, as the user only needs to download the code necessary for the initial route.

React provides two core features to implement code splitting with minimal effort: `React.lazy` for defining components that should be loaded dynamically, and `React.Suspense` for specifying a loading state while those components are being fetched.

## `React.lazy()`

The `React.lazy` function lets you render a dynamically imported component as a regular component. It takes a function that must call a dynamic `import()` as its argument. This `import()` must return a `Promise` which resolves to a module with a `default` export containing a React component.

### Basic Usage

Before code splitting, you might import a component statically:

```javascript
import MyComponent from './MyComponent';

const App = () => (
  <div>
    <MyComponent />
  </div>
);
```

With `React.lazy`, you can defer loading this component's code until it's actually rendered:

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

The `lazy` function returns a special `LazyComponent` object. This object has a `$$typeof` of `REACT_LAZY_TYPE` and contains a `_payload` with the loading status and the result, along with an `_init` function (`lazyInitializer`) that React calls to resolve the component.

## `React.Suspense`

A component defined with `React.lazy` must be rendered inside a `Suspense` component. `Suspense` allows you to specify a loading indicator (a fallback UI) that is shown while the lazy component is loading.

The `fallback` prop accepts any React elements that you want to render while waiting for the component to load.

```javascript
<Suspense fallback={<p>Please wait, the component is loading.</p>}>
  <LazyLoadedComponent />
</Suspense>
```

You can place a single `Suspense` component high up in the component tree to handle loading states for multiple lazy components, or you can wrap individual lazy components for more granular control.

## How It Works Internally

When React first attempts to render a `LazyComponent`, it triggers the `lazyInitializer` function. This process involves several internal states:

| State | Value | Description |
|---|---|---|
| `Uninitialized` | -1 | The initial state before the dynamic import has been called. |
| `Pending` | 0 | The dynamic `import()` has been called, and the promise is in flight. |
| `Resolved` | 1 | The promise successfully resolved, and the component module is available. |
| `Rejected` | 2 | The promise was rejected, indicating a loading error. |

Here is a diagram illustrating the lifecycle of a lazy-loaded component with `Suspense`.

```d2
direction: down

App-Render: {
  label: "App Renders"
  shape: rectangle
}

Suspense-Boundary: {
  label: "<Suspense> Boundary"
  shape: package

  Lazy-Component-Render: {
    label: "<LazyComponent> is rendered"
    shape: rectangle
  }

  Show-Fallback: {
    label: "Displays Fallback UI"
    shape: rectangle
    style.stroke: "#faad14"
  }
}

Network: {
  label: "Network"
  shape: cylinder

  Dynamic-Import: {
    label: "dynamic import() promise"
    shape: rectangle
  }
}

Render-Result: {
  label: "Final Render"
  shape: package

  Render-Component: {
    label: "Renders Actual Component"
    shape: rectangle
    style.stroke: "#52c41a"
  }

  Throw-Error: {
    label: "Throws an Error"
    shape: rectangle
    style.stroke: "#ff4d4f"
  }
}

Error-Boundary: {
  label: "Error Boundary"
  shape: rectangle
  tooltip: "Catches rendering errors"
}

App-Render -> Suspense-Boundary.Lazy-Component-Render: "1. Initial render attempt"
Suspense-Boundary.Lazy-Component-Render -> Network.Dynamic-Import: "2. Triggers lazyInitializer, status becomes Pending"
Suspense-Boundary.Lazy-Component-Render -> Suspense-Boundary.Show-Fallback: "3. React suspends rendering"

Network.Dynamic-Import -> Render-Result.Render-Component: "4a. Promise resolves (status: Resolved)"
Network.Dynamic-Import -> Render-Result.Throw-Error: "4b. Promise rejects (status: Rejected)"

Render-Result.Throw-Error -> Error-Boundary: "5. Error is caught"

```

1.  When React encounters the lazy component, its status is `Uninitialized`. React calls the `_init` function.
2.  The `_init` function executes the dynamic `import()` and transitions the component's status to `Pending`. It then throws the promise (`thenable`) that the `import()` call returns.
3.  Because the component is not ready, React suspends the rendering process. It travels up the component tree until it finds the nearest `<Suspense>` boundary, which catches the promise and displays its `fallback` UI.
4.  Once the promise resolves successfully, the lazy component's status changes to `Resolved`. React is notified and attempts to render the component again. This time, the `_init` function sees the `Resolved` status and returns the actual component module's `default` export, which is then rendered on the screen.
5.  If the promise rejects, the status becomes `Rejected`. The `_init` function throws the error. This error will propagate up the tree and should be caught by an [Error Boundary](https://react.dev/reference/react/Component#catching-rendering-errors-with-an-error-boundary).

## Error Handling

If the lazy component fails to load (for example, due to a network error), it will trigger an error. You can handle these errors gracefully and display a user-friendly message by wrapping your lazy component in an Error Boundary. An Error Boundary is a class component that catches JavaScript errors anywhere in its child component tree.

```javascript
import React, { Suspense } from 'react';
import ErrorBoundary from './ErrorBoundary'; // A custom Error Boundary component

const OtherComponent = React.lazy(() => import('./OtherComponent'));

function MyApp() {
  return (
    <div>
      <ErrorBoundary>
        <Suspense fallback={<div>Loading...</div>}>
          <OtherComponent />
        </Suspense>
      </ErrorBoundary>
    </div>
  );
}
```

By combining `lazy`, `Suspense`, and Error Boundaries, you can create a robust and performant user experience that handles loading states and network failures effectively.