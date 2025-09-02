# Code Splitting with `lazy` and `Suspense`

Code splitting is a powerful technique for optimizing React applications. It allows you to split your code into smaller chunks, which can then be loaded on demand when they are actually needed by the user. This reduces the initial bundle size, leading to faster page loads and an improved user experience. React provides a built-in way to implement code splitting using `React.lazy` and `React.Suspense`.

This guide covers how to use these APIs to dynamically load components.

## Dynamically Loading Components with `React.lazy`

The `React.lazy` function provides a simple way to render a dynamically imported component as if it were a regular component. It's available for both client and server environments.

Before, you might import a component statically:
```javascript
import MyComponent from './MyComponent';
```

With `lazy`, you change this to a dynamic import that returns a Promise:

```javascript
import React, { lazy } from 'react';

const MyComponent = lazy(() => import('./MyComponent'));
```

`React.lazy` takes a function that must call a dynamic `import()`. This `import()` call returns a Promise that resolves to a module with a `default` export containing the React component. If the `default` export is missing, React will throw an error during the render phase.

### How `lazy` Works Internally

A `lazy` component has an internal state machine to manage the loading process. The component's payload transitions through several statuses:

```d2
direction: right

Uninitialized: {
  label: "Uninitialized"
}
Pending: {
  label: "Pending"
  "Dynamic import() is called"
}
Resolved: {
  label: "Resolved"
  "Promise fulfills, component renders"
}
Rejected: {
  label: "Rejected"
  "Promise rejects, error is thrown"
}

Uninitialized -> Pending: "First render attempt"
Pending -> Resolved: "Network request succeeds"
Pending -> Rejected: "Network request fails"
```

This state is managed by an initializer function that handles the Promise returned by the dynamic import, transitioning the component from `Pending` to either `Resolved` or `Rejected`.

## Handling Loading States with `Suspense`

A lazy component will suspend rendering while it's being loaded. `React.Suspense` allows you to specify a loading indicator (a fallback UI) to be displayed while the component is in the `Pending` state. 

```javascript
import React, { Suspense, lazy } from 'react';

const OtherComponent = lazy(() => import('./OtherComponent'));

function App() {
  return (
    <div>
      <h1>My Application</h1>
      <Suspense fallback={<div>Loading component...</div>}>
        <OtherComponent />
      </Suspense>
    </div>
  );
}
```

The `fallback` prop accepts any renderable React elements. You can place the `Suspense` component anywhere above the lazy component, allowing you to wrap multiple lazy components with a single loading indicator.

## Route-Based Code Splitting

A common and effective use case for code splitting is with routing. You can load the code for different pages or sections of your application only when the user navigates to them.

Here is a conceptual example:

```javascript
import React, { Suspense, lazy } from 'react';
// Assuming a basic router component
import { Router, Route } from './my-router';

const HomePage = lazy(() => import('./routes/Home'));
const AboutPage = lazy(() => import('./routes/About'));

const App = () => (
  <Router>
    <Suspense fallback={<div>Loading page...</div>}>
      <Route path="/" component={HomePage} />
      <Route path="/about" component={AboutPage} />
    </Suspense>
  </Router>
);
```

In this setup, the JavaScript for `HomePage` is only fetched when a user visits the root path, and the code for `AboutPage` is only fetched when they navigate to `/about`.

## Error Handling with Error Boundaries

If the dynamic import fails (for example, due to a network error), it will throw an error. To handle these errors gracefully and show a user-friendly message, you can wrap your lazy components in an [Error Boundary](https://react.dev/reference/react/Component#catching-rendering-errors-with-an-error-boundary).

An Error Boundary is a class component that defines `static getDerivedStateFromError()` or `componentDidCatch()`.

```javascript
import React, { Suspense, lazy } from 'react';
import { MyErrorBoundary } from './MyErrorBoundary';

const BrokenComponent = lazy(() => import('./NonExistentComponent'));

function App() {
  return (
    <div>
      <MyErrorBoundary>
        <Suspense fallback={<div>Loading...</div>}>
          <BrokenComponent />
        </Suspense>
      </MyErrorBoundary>
    </div>
  );
}
```

By placing an Error Boundary around `Suspense`, you can catch failures from dynamic imports and display a fallback error UI, preventing the entire application from crashing.

---

By leveraging `lazy` and `Suspense`, you can significantly improve the performance and user experience of your React applications. For more advanced topics, you may be interested in learning about the differences between [Server vs. Client Environments](./advanced-server-vs-client.md) and how React handles [Caching](./advanced-caching.md).