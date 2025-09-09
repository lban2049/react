# Code Splitting with `lazy` and `Suspense`

Code-splitting is a powerful technique for optimizing web application performance. It involves splitting your code into smaller chunks that can be loaded on demand, rather than downloading the entire application bundle when a user first visits a page. This leads to faster initial load times, especially for large applications.

React provides a simple yet powerful way to implement code-splitting for components using `React.lazy` and `React.Suspense`. This combination allows you to render a dynamically imported component as if it were a regular, statically imported one, while showing a fallback UI (like a loading spinner) until the component is ready.

## Core Concepts

### `React.lazy`

The `React.lazy` function lets you define a component that is loaded dynamically. It takes a function as its argument that must call a dynamic `import()`. This `import()` call returns a `Promise` that resolves to a module with a `default` export containing the React component.

**Syntax**

```javascript icon=logos:react
import React, { lazy } from 'react';

const MyLazyComponent = lazy(() => import('./MyLazyComponent'));
```

Behind the scenes, `React.lazy` creates a special `LazyComponent` object. This object has an internal state to track whether the dynamic import is `Pending`, `Resolved`, or `Rejected`. When React first tries to render this component, it triggers the dynamic import and "suspends" rendering until the promise resolves.

### `React.Suspense`

A lazy component can only be rendered inside a `React.Suspense` component boundary. `Suspense` allows you to specify a loading indicator (the `fallback`) that is displayed while the lazy component is being fetched and loaded.

**Syntax**

```javascript icon=logos:react
import React, { Suspense } from 'react';

const MyLazyComponent = React.lazy(() => import('./MyLazyComponent'));

function App() {
  return (
    <div>
      <h1>My Application</h1>
      <Suspense fallback={<div>Loading...</div>}>
        <MyLazyComponent />
      </Suspense>
    </div>
  );
}
```

In this example, the `<div>Loading...</div>` will be rendered in place of `MyLazyComponent` until the code for `MyLazyComponent.js` has been downloaded and is ready to be displayed.

## How It Works: The Loading Flow

The interaction between `lazy` and `Suspense` creates a seamless user experience for on-demand loading. Here is a diagram illustrating the process:

```d2 The Lazy Loading Process
direction: down

User: { 
  shape: c4-person 
}

React-App: {
  label: "React Application"
  shape: rectangle

  Suspense-Boundary: {
    label: "<Suspense>"
    shape: rectangle

    Lazy-Component: {
      label: "<LazyComponent>"
      shape: rectangle
    }
  }
}

Server: {
  label: "Web Server"
  shape: cylinder
}

User -> React-App.Suspense-Boundary.Lazy-Component: "1. Triggers render of lazy component"
React-App.Suspense-Boundary.Lazy-Component -> Server: "2. Initiates dynamic import() to fetch component chunk"
React-App.Suspense-Boundary -> User: "3. Suspends render and shows fallback UI (e.g., spinner)"
Server -> React-App.Suspense-Boundary.Lazy-Component: "4. Component code downloads"
React-App.Suspense-Boundary.Lazy-Component -> React-App.Suspense-Boundary.Lazy-Component: "5. Promise resolves, component is ready"
React-App.Suspense-Boundary.Lazy-Component -> User: "6. React renders the actual component, replacing the fallback"

```

## API Reference: `lazy()`

The `lazy` function is your primary tool for creating code-split components.

### `lazy(ctor)`

-   **Parameters**
    -   `ctor`: A function that returns a `Promise`. This function is executed only when the component is first rendered. The promise it returns should resolve to a module object that has a `default` export containing a React component.
-   **Returns**
    -   A React component that you can render in your tree. While it's loading, it will suspend and trigger the nearest `<Suspense>` boundary's fallback.

#### Internal Structure

The object returned by `lazy` is a special type with the following key properties, as defined in React's source:

| Property | Description |
|---|---|
| `$$typeof` | A symbol (`REACT_LAZY_TYPE`) that identifies it as a lazy component. |
| `_payload` | An object that holds the loading state (`_status`) and the result (`_result`). The status can be `Uninitialized`, `Pending`, `Resolved`, or `Rejected`. |
| `_init` | A function (`lazyInitializer`) that React calls to start the loading process and handle the promise lifecycle. |

## Common Use Cases

### Route-Based Code Splitting

One of the most effective places to introduce code-splitting is at the route level. By lazy-loading the components for each page, you ensure that users only download the code for the specific page they are visiting.

```javascript Route-based Splitting Example icon=logos:react
import React, { Suspense, lazy } from 'react';
import { BrowserRouter as Router, Routes, Route } from 'react-router-dom';

const Home = lazy(() => import('./routes/Home'));
const About = lazy(() => import('./routes/About'));

const App = () => (
  <Router>
    <Suspense fallback={<div>Loading page...</div>}>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
      </Routes>
    </Suspense>
  </Router>
);
```

### Conditional Component Loading

You can also lazy-load components that are only rendered based on user interaction or some other condition. This is useful for heavy components like modals, complex charts, or special editors that aren't needed on the initial render.

```javascript Conditional Component Example icon=logos:react
import React, { useState, Suspense, lazy } from 'react';

const HeavyChartComponent = lazy(() => import('./HeavyChartComponent'));

function Dashboard() {
  const [showChart, setShowChart] = useState(false);

  return (
    <div>
      <button onClick={() => setShowChart(true)}>Show Chart</button>
      {showChart && (
        <Suspense fallback={<div>Loading chart...</div>}>
          <HeavyChartComponent />
        </Suspense>
      )}
    </div>
  );
}
```

By combining `React.lazy` and `Suspense`, you can significantly improve your application's load performance and provide a better user experience. 

After optimizing your component loading, you might want to explore how to manage complex UI updates smoothly. Learn more in the [Transitions](./advanced-transitions.md) section.