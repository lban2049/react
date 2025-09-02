# Components and Elements

This section provides comprehensive documentation for React's built-in components and essential utilities used for creating and manipulating React elements. These APIs are fundamental for defining the structure and behavior of your client-side applications.

For an in-depth understanding of how components manage their state and side effects, refer to the [Hooks Reference](./client-apis-hooks.md). To grasp the foundational concepts behind components and JSX, visit the [Core Concepts](./core-concepts.md) section.

## Base Component Classes

React provides two primary base classes for defining components: `Component` and `PureComponent`. These are used when building class-based components.

### `Component`

`Component` is the base class for React class components. Components defined using this class maintain their own state and lifecycle methods, which allow for fine-grained control over rendering behavior.

#### `new Component(props, context, updater)`

The constructor for `Component` initializes a new component instance with the given `props`, `context`, and an `updater` object (which handles state updates).

#### `component.setState(partialState, callback)`

`setState` is the primary method for updating a component's state. When state changes, React re-renders the component. It can be called with either an object or a function.

It is important to treat `this.state` as immutable. There is no guarantee that `this.state` will be immediately updated after calling `setState`, as updates may be batched for performance.

**Parameters**

| Name | Type | Description |
|---|---|---|
| `partialState` | `object` \| `function` | An object containing the new state to merge with the current state, or a function that receives the previous state and props and returns an object of state variables to update. |
| `callback` | `?function` | An optional callback function that will be executed after the state update is completed and the component has re-rendered. |

**Example**

```javascript
import React from 'react';

class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
  }

  incrementCount = () => {
    this.setState(prevState => ({
      count: prevState.count + 1
    }), () => {
      console.log('State updated to:', this.state.count);
    });
  };

  render() {
    return (
      <button onClick={this.incrementCount}>
        Count: {this.state.count}
      </button>
    );
  }
}

// Usage example (e.g., in a root component)
// function App() {
//   return <MyComponent />;
// }
// export default App;
```

This example demonstrates updating the `count` state using `setState` with a functional update, and logging the new state after the update is applied.

#### `component.forceUpdate(callback)`

`forceUpdate` forces a component to re-render, bypassing `shouldComponentUpdate`. This method should be used sparingly and only when you know that some deeper aspect of the component's state or props has changed but `setState` was not called.

**Parameters**

| Name | Type | Description |
|---|---|---|
| `callback` | `?function` | An optional callback function that will be executed after the component update is completed. |

**Example**

```javascript
import React from 'react';

class DataDisplay extends React.Component {
  constructor(props) {
    super(props);
    this.data = { value: 'Initial' };
  }

  // Simulate direct mutation (generally discouraged)
  mutateDataAndForceUpdate = () => {
    this.data.value = 'Updated via forceUpdate at ' + new Date().toLocaleTimeString();
    this.forceUpdate(() => {
      console.log('Component forced updated. Displaying:', this.data.value);
    });
  };

  render() {
    return (
      <div>
        <p>Current Data: {this.data.value}</p>
        <button onClick={this.mutateDataAndForceUpdate}>
          Force Update
        </button>
      </div>
    );
  }
}

// Usage example
// function App() {
//   return <DataDisplay />;
// }
// export default App;
```

This example shows `forceUpdate` being used after a direct (and generally discouraged) mutation of a component instance property. `forceUpdate` explicitly triggers a re-render.

### `PureComponent`

`PureComponent` is similar to `Component` but includes a shallow prop and state comparison in its `shouldComponentUpdate` method. This means `PureComponent` will re-render only if its props or state have shallowly changed, potentially offering performance benefits by avoiding unnecessary re-renders for components with simple props and state.

**Example**

```javascript
import React from 'react';

class OptimizedDisplay extends React.PureComponent {
  render() {
    console.log('OptimizedDisplay rendered');
    return (
      <div>
        <p>Value: {this.props.value}</p>
      </div>
    );
  }
}

// When parent re-renders but props.value doesn't shallowly change,
// OptimizedDisplay will not re-render.
// function App() {
//   const [data, setData] = React.useState({ value: 'hello' });
//   React.useEffect(() => {
//     const interval = setInterval(() => {
//       // This will cause App to re-render, but OptimizedDisplay will not
//       // re-render if data.value is shallowly equal.
//       setData(prev => ({ ...prev })); 
//     }, 1000);
//     return () => clearInterval(interval);
//   }, []);
//
//   return <OptimizedDisplay value={data.value} />;
// }
// export default App;
```

In this example, `OptimizedDisplay` will only re-render if its `value` prop changes, thanks to `PureComponent`'s shallow comparison.

## Core Element Factories

React provides functions to create and manipulate elements, which are the building blocks of your UI.

### `createElement(type, props, ...children)`

`createElement` is the core function for creating React elements. While often abstracted by JSX syntax, it can be called directly to construct elements programmatically. An element is a lightweight description of what you want to render.

**Parameters**

| Name | Type | Description |
|---|---|---|
| `type` | `string` \| `function` \| `object` | The type of the element. This can be an HTML tag name string (e.g., `'div'`, `'p'`), a React component class, a React function component, or a React symbol (e.g., `React.Fragment`). |
| `props` | `?object` | An object containing the props to be passed to the element. This can include `key` and `ref` special props. |
| `...children` | `any` | Zero or more child elements or values to be rendered inside the created element. |

**Returns**

| Name | Type | Description |
|---|---|---|
| `element` | `ReactElement` | A new React element object. |

**Example**

```javascript
import React from 'react';

const MyGreeting = ({ name }) => React.createElement('h1', null, `Hello, ${name}!`);

// Creating a simple div element
const divElement = React.createElement(
  'div',
  { className: 'container' },
  'This is some text',
  React.createElement('p', null, 'And a paragraph.')
);

// Creating a component element
const greetingElement = React.createElement(MyGreeting, { name: 'World' });

// Example of how these elements might be used within a component render method:
// function App() {
//   return (
//     <div>
//       {divElement}
//       {greetingElement}
//     </div>
//   );
// }
// export default App;
```

This example demonstrates creating a `div` element with children and creating an element from a functional component using `createElement`.

### `cloneElement(element, props, ...children)`

`cloneElement` is used to create a new React element using an existing element as a starting point. It allows you to override or add new props, `key`, and `ref` to the cloned element, while preserving the original element's `type` and `_owner`.

**Parameters**

| Name | Type | Description |
|---|---|---|
| `element` | `ReactElement` | The existing React element to clone. |
| `props` | `?object` | An object containing new props to merge with the original element's props. New props will override existing ones. |
| `...children` | `any` | New children to be rendered inside the cloned element. These will replace the original children. |

**Returns**

| Name | Type | Description |
|---|---|---|
| `clonedElement` | `ReactElement` | A new React element, a clone of the original with updated props and children. |

**Example**

```javascript
import React from 'react';

function Greeting({ message, style }) {
  return <h1 style={style}>{message}</h1>;
}

const originalGreeting = <Greeting message="Hello" style={{ color: 'blue' }} />;

// Clone the original greeting, changing the message and adding a new style
const clonedGreeting = React.cloneElement(
  originalGreeting,
  { message: 'Greetings from React!', style: { color: 'green', fontSize: '24px' } },
  // Children can also be passed here if the original element accepted them
);

// Example of usage:
// function App() {
//   return (
//     <div>
//       {originalGreeting}
//       {clonedGreeting}
//     </div>
//   );
// }
// export default App;
```

This example clones an existing `Greeting` element, changing its `message` prop and adding a new style.

### `isValidElement(object)`

`isValidElement` checks if a given object is a valid React element. This is useful for type checking, especially when dealing with children or dynamically created content.

**Parameters**

| Name | Type | Description |
|---|---|---|
| `object` | `any` | The object to check. |

**Returns**

| Name | Type | Description |
|---|---|---|
| `boolean` | `boolean` | `true` if the object is a React element; otherwise, `false`. |

**Example**

```javascript
import React from 'react';

const myElement = <div>Hello</div>;
const myObject = { value: 123 };

console.log(React.isValidElement(myElement)); // true
console.log(React.isValidElement(myObject));  // false
console.log(React.isValidElement(null));     // false
console.log(React.isValidElement('string')); // false

// Example of usage within a component:
// function ChildChecker({ children }) {
//   return (
//     <div>
//       {React.isValidElement(children) ? (
//         <p>Child is a valid React element.</p>
//       ) : (
//         <p>Child is NOT a valid React element.</p>
//       )}
//     </div>
//   );
// }
// function App() {
//   return (
//     <div>
//       <ChildChecker><span>Test</span></ChildChecker>
//       <ChildChecker>{123}</ChildChecker>
//     </div>
//   );
// }
// export default App;
```

This example demonstrates how `isValidElement` distinguishes between React elements and other JavaScript objects or primitives.

## Built-in Special Components

React includes several special components that provide specific behaviors or development-time utilities.

### `Fragment`

`Fragment` allows you to group a list of children without adding extra nodes to the DOM. This can be useful when you need to return multiple elements from a component but don't want to wrap them in an unnecessary `div`.

**Example**

```javascript
import React, { Fragment } from 'react';

function ListItem() {
  return (
    <Fragment>
      <td>Item 1</td>
      <td>Item 2</td>
    </Fragment>
  );
}

// Using shorthand syntax:
function AnotherListItem() {
  return (
    <>
      <td>Item A</td>
      <td>Item B</td>
    </>
  );
}

// Example usage within a table:
// function App() {
//   return (
//     <table>
//       <tbody>
//         <tr>
//           <ListItem />
//         </tr>
//         <tr>
//           <AnotherListItem />
//         </tr>
//       </tbody>
//     </table>
//   );
// }
// export default App;
```

This example shows how `Fragment` (or its shorthand `<></>`) can be used to return multiple `td` elements without wrapping them in an extra DOM node, which is essential for valid HTML table structures.

### `Profiler`

`Profiler` is a component used to measure rendering performance of a React tree. It collects timing information about how often a React application renders and what the components within it render, and how long it takes. It can be placed anywhere in a React tree to measure the performance of its descendants.

**Parameters**

| Name | Type | Description |
|---|---|---|
| `id` | `string` | A string that identifies the part of the UI you are profiling. |
| `onRender` | `function` | A callback function that React calls whenever a component within the profiled tree "commits" an update. |

**Example**

```javascript
import React, { Profiler } from 'react';

function onRenderCallback(
  id, // the "id" prop of the Profiler tree that has just committed
  phase, // either "mount" (if the tree just mounted) or "update" (if it re-rendered)
  actualDuration, // time spent rendering the committed update
  baseDuration, // estimated time to render the entire subtree without memoization
  startTime, // when React began rendering this update
  commitTime, // when React committed this update
  interactions // Set of interactions belonging to this update
) {
  // Aggregate or log render timings...
  console.log(`Profiler ${id} rendered: phase=${phase}, actualDuration=${actualDuration.toFixed(2)}ms`);
}

// Example usage:
// function App() {
//   return (
//     <Profiler id="ApplicationRoot" onRender={onRenderCallback}>
//       <MyComponent />
//       <AnotherComponent />
//     </Profiler>
//   );
// }
// export default App;
```

This example demonstrates how to use `Profiler` with a `onRender` callback to log performance metrics for a part of your application.

### `StrictMode`

`StrictMode` is a tool for highlighting potential problems in a React application during development. Like `Fragment`, `StrictMode` does not render any visible UI. It activates additional checks and warnings for its descendants, which are run in development mode only and do not impact the production build.

**Example**

```javascript
import React from 'react';

function App() {
  return (
    <React.StrictMode>
      <MyLegacyComponent />
    </React.StrictMode>
  );
}

// MyLegacyComponent might use deprecated lifecycle methods or have other potential issues.
// StrictMode will warn about these in the console during development.
```

This example shows how to wrap components with `StrictMode` to enable development-only warnings and checks.

### `Suspense`

`Suspense` lets your components "wait" for something before rendering, such as lazily loaded code using `React.lazy` or data loaded from a network request. It provides a way to gracefully handle loading states in your UI.

**Parameters**

| Name | Type | Description |
|---|---|---|
| `fallback` | `React.Node` | A React element that will be rendered while the children are loading. This can be any React node, such as a loading spinner or a placeholder UI. |

**Example**

```javascript
import React, { Suspense, lazy } from 'react';

const LazyComponent = lazy(() => import('./LazyComponent'));

function App() {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <LazyComponent />
    </Suspense>
  );
}

// LazyComponent.js (separate file)
// export default function LazyComponent() {
//   return <h2>I'm a lazily loaded component!</h2>;
// }
```

This example demonstrates using `Suspense` to display a loading indicator while `LazyComponent` (loaded via `React.lazy`) is being fetched.

### `unstable_SuspenseList`

`unstable_SuspenseList` is an experimental component that orchestrates how multiple `Suspense` and `unstable_LegacyHidden` components in a list are revealed to the user. It allows you to define a sequence or a group for their appearance, improving the user experience for loading multiple items.

**Parameters**

| Name | Type | Description |
|---|---|---|
| `revealOrder` | `string` | Defines the order in which items in the list should be revealed. Can be `'forwards'`, `'backwards'`, or `'together'`. |
| `tail` | `string` | Defines whether to show all fallbacks (`'collapsed'`) or only the next one (`'hidden'`). Can be `'collapsed'` or `'hidden'`. |
| `children` | `React.Node` | Multiple `Suspense` components or other components that can suspend. |

**Example**

```javascript
import React, { Suspense, unstable_SuspenseList, lazy } from 'react';

const ComponentA = lazy(() => import('./ComponentA'));
const ComponentB = lazy(() => import('./ComponentB'));
const ComponentC = lazy(() => import('./ComponentC'));

function Dashboard() {
  return (
    <unstable_SuspenseList revealOrder="forwards" tail="collapsed">
      <Suspense fallback={<div>Loading A...</div>}>
        <ComponentA />
      </Suspense>
      <Suspense fallback={<div>Loading B...</div>}>
        <ComponentB />
      </Suspense>
      <Suspense fallback={<div>Loading C...</div>}>
        <ComponentC />
      </Suspense>
    </unstable_SuspenseList>
  );
}
```

This example illustrates `unstable_SuspenseList` revealing `ComponentA`, `ComponentB`, and `ComponentC` in a forwards order, with fallbacks collapsing as items load.

### `unstable_LegacyHidden`

`unstable_LegacyHidden` is an experimental component used to hide content from the user without unmounting it, thereby preserving its state and DOM. This can be useful for offscreen navigation or tab views where you want to keep inactive tabs mounted for quicker re-appearance.

**Parameters**

| Name | Type | Description |
|---|---|---|
| `mode` | `string` | Specifies the visibility mode of the content. Can be `'hidden'` to hide the content, or `'visible'` to show it. |
| `children` | `React.Node` | The content to be conditionally hidden or shown. |

**Example**

```javascript
import React, { useState, unstable_LegacyHidden } from 'react';

function TabComponent() {
  const [activeTab, setActiveTab] = useState('tab1');

  return (
    <div>
      <button onClick={() => setActiveTab('tab1')}>Tab 1</button>
      <button onClick={() => setActiveTab('tab2')}>Tab 2</button>

      <unstable_LegacyHidden mode={activeTab === 'tab1' ? 'visible' : 'hidden'}>
        <div>Content for Tab 1</div>
      </unstable_LegacyHidden>
      <unstable_LegacyHidden mode={activeTab === 'tab2' ? 'visible' : 'hidden'}>
        <div>Content for Tab 2</div>
      </unstable_LegacyHidden>
    </div>
  );
}
```

This example uses `unstable_LegacyHidden` to switch between tab content, keeping the inactive tab's components mounted but hidden.

### `unstable_Activity`

`unstable_Activity` is an experimental component related to tracking user interaction and activity states within a React tree. It is typically used for internal performance monitoring or analytics, especially in concurrent rendering scenarios where the active state of parts of the UI might change dynamically.

**Parameters**

| Name | Type | Description |
|---|---|---|
| `mode` | `string` | The activity mode of the wrapped content, typically `'visible'` or `'hidden'`. |
| `children` | `React.Node` | The content whose activity state is being tracked. |

**Example**

```javascript
import React, { unstable_Activity } from 'react';

function UserActivityTracker({ children }) {
  // In a real application, this component might integrate with an analytics system
  // or dispatch events based on the visibility/activity of its children.
  const onVisibilityChange = (isVisible) => {
    console.log(`Content is now ${isVisible ? 'visible' : 'hidden'}`);
  };

  return (
    <unstable_Activity mode="visible" /* mode could be dynamically set */>
      {children}
    </unstable_Activity>
  );
}
```

This conceptual example demonstrates how `unstable_Activity` could wrap a component to track its visibility or active state.

### `unstable_Scope`

`unstable_Scope` is an experimental feature designed to create a distinct rendering scope within the React tree. This can be relevant for internal optimizations or specific rendering strategies where isolated rendering environments are beneficial.

**Example**

```javascript
import React, { unstable_Scope } from 'react';

function MyScopedComponent() {
  return (
    <unstable_Scope>
      {/* Components rendered within this scope might benefit from specific rendering behaviors or optimizations. */}
      <p>Content inside a custom scope.</p>
    </unstable_Scope>
  );
}
```

This example shows a basic usage of `unstable_Scope` to define a distinct rendering boundary.

### `unstable_TracingMarker`

`unstable_TracingMarker` is an experimental component that allows developers to mark specific sections of their UI for performance tracing and debugging. It is particularly useful for understanding the performance of transitions and identifying rendering bottlenecks within marked areas.

**Parameters**

| Name | Type | Description |
|---|---|---|
| `name` | `string` | A descriptive name for the tracing marker, used in performance tools. |
| `onAllReady` | `?function` | An optional callback function that is invoked when all content within the marked section has finished loading or rendering. |
| `children` | `React.Node` | The UI section to be traced. |

**Example**

```javascript
import React, { unstable_TracingMarker } from 'react';

function ProductPage() {
  const handleAllReady = () => {
    console.log('Product page content fully loaded and rendered.');
  };

  return (
    <unstable_TracingMarker name="ProductPageLoad" onAllReady={handleAllReady}>
      {/* Components related to product details and reviews */}
      <ProductDetails />
      <ProductReviews />
    </unstable_TracingMarker>
  );
}
```

This example demonstrates using `unstable_TracingMarker` to measure and log when a product page's content is fully ready.

### `unstable_ViewTransition`

`unstable_ViewTransition` is an experimental component designed to integrate with the browser's View Transitions API, enabling smoother and more visually appealing transitions between different UI states without complex manual animations. It allows the browser to handle the cross-fade or custom animations between different views.

**Parameters**

| Name | Type | Description |
|---|---|---|
| `children` | `React.Node` | The content that will participate in the view transition. This content will be captured and animated during the transition. |

**Example**

```javascript
import React, { unstable_ViewTransition, useState } from 'react';

function Gallery() {
  const [showDetails, setShowDetails] = useState(false);

  return (
    <unstable_ViewTransition>
      {showDetails ? (
        <ProductDetails />
      ) : (
        <ThumbnailGrid />
      )}
      <button onClick={() => setShowDetails(!showDetails)}>Toggle Details</button>
    </unstable_ViewTransition>
  );
}

// ProductDetails and ThumbnailGrid would be your regular React components.
```

This example shows how `unstable_ViewTransition` can wrap conditionally rendered components to enable smooth visual transitions when their state changes.

## Utility Functions

In addition to components, React provides several utility functions that help with common tasks related to elements and context.

### `createRef()`

`createRef` returns a ref object that can be attached to React elements via the `ref` attribute. This provides a way to access DOM nodes or React components created in the `render` method, allowing for direct interaction or imperative actions.

**Parameters**

None.

**Returns**

| Name | Type | Description |
|---|---|---|
| `refObject` | `RefObject` | An object with a single mutable property `current`, initialized to `null`. This `current` property will hold the DOM element or component instance after the component mounts. |

**Example**

```javascript
import React, { createRef, Component } from 'react';

class MyForm extends Component {
  constructor(props) {
    super(props);
    this.textInput = createRef();
  }

  focusTextInput = () => {
    // Directly access the DOM node via the ref to focus the input
    if (this.textInput.current) {
      this.textInput.current.focus();
    }
  };

  render() {
    return (
      <div>
        <input type="text" ref={this.textInput} />
        <button onClick={this.focusTextInput}>Focus the text input</button>
      </div>
    );
  }
}
```

This example demonstrates how to create a ref using `createRef` and attach it to an input element to programmatically focus it.

### `createContext(defaultValue)`

`createContext` creates a Context object. When React renders a component that subscribes to this Context object, it will read the current Context value from the closest matching `Provider` above it in the component tree. This allows data to be passed deeply through the component tree without manually passing props at every level.

**Parameters**

| Name | Type | Description |
|---|---|---|
| `defaultValue` | `any` | The default value for the context. This value is used when a component consumes the context without a matching Provider above it in the tree, or when rendering without a Provider. |

**Returns**

| Name | Type | Description |
|---|---|---|
| `context` | `ReactContext` | A Context object. This object comes with a `Provider` React component that allows consuming components to subscribe to context changes, and a `Consumer` component (or typically used with `useContext` hook). |

**Example**

```javascript
import React, { createContext, useContext } from 'react';

// 1. Create a Context object
const ThemeContext = createContext('light'); // 'light' is the default value

// 2. Provider component to supply the context value
function ThemeProvider({ children }) {
  const theme = 'dark'; // In a real application, this would typically come from component state or props
  return (
    <ThemeContext.Provider value={theme}>
      {children}
    </ThemeContext.Provider>
  );
}

// 3. Consumer component to read the context value
function ThemedButton() {
  const theme = useContext(ThemeContext); // Using the useContext hook to read context
  return (
    <button style={{ background: theme === 'dark' ? 'black' : 'white', color: theme === 'dark' ? 'white' : 'black' }}>
      My themed button
    </button>
  );
}

// Example usage within your application:
// function App() {
//   return (
//     <ThemeProvider>
//       <ThemedButton />
//     </ThemeProvider>
//   );
// }
// export default App;
```

This example illustrates the basic usage of `createContext` to establish a theme that can be provided by `ThemeProvider` and consumed by `ThemedButton`.

### `lazy(ctor)`

`lazy` lets you define a component that is loaded dynamically (lazily). This helps to reduce the initial bundle size of your application by only loading the necessary components when they are actually rendered. A lazy component must always be rendered inside a `Suspense` component.

**Parameters**

| Name | Type | Description |
|---|---|---|
| `ctor` | `() => Thenable<{default: T, ...}>` | A function that returns a Promise (or a thenable) that resolves to a module object. The module object must have a `default` export that is a React component. |

**Returns**

| Name | Type | Description |
|---|---|---|
| `LazyComponent` | `LazyComponent<T, Payload<T>>` | A React component type that will load the given component lazily when it is rendered. |

**Example**

```javascript
import React, { Suspense, lazy } from 'react';

// Define a lazy-loaded component. 'DetailComponent.js' would contain the actual component export.
const LazyLoadedComponent = lazy(() => import('./DetailComponent'));

function App() {
  const [showDetail, setShowDetail] = React.useState(false);

  return (
    <div>
      <button onClick={() => setShowDetail(!showDetail)}>
        Toggle Detail Component
      </button>
      {showDetail && (
        <Suspense fallback={<div>Loading Detail...</div>}>
          <LazyLoadedComponent />
        </Suspense>
      )}
    </div>
  );
}

// Example of DetailComponent.js (in a separate file)
// export default function DetailComponent() {
//   return <p>This content was loaded on demand!</p>;
// }
```

This example demonstrates how to use `lazy` to load `DetailComponent` only when `showDetail` is true, providing a `Suspense` fallback while it loads.

### `forwardRef(render)`

`forwardRef` lets your component receive a `ref` prop and forward it to a child component, specifically to a DOM node or a class component instance that it renders. This is useful when you want a parent component to imperatively access the underlying DOM element or component instance of its child.

**Parameters**

| Name | Type | Description |
|---|---|---|
| `render` | `(props, ref) => React.Node` | A render function that receives `props` and the `ref` as its two arguments, and returns a React node (e.g., a JSX element). |

**Returns**

| Name | Type | Description |
|---|---|---|
| `elementType` | `object` | A React component type that can receive a `ref` prop and forward it. |

**Example**

```javascript
import React, { forwardRef, useRef } from 'react';

// MyInput is a functional component that forwards its ref to the native input element.
const MyInput = forwardRef((props, ref) => (
  <input type="text" ref={ref} {...props} />
));

function App() {
  const inputRef = useRef(null); // Create a ref using the useRef hook

  const handleClick = () => {
    // When the button is clicked, focus the input element via its ref.
    if (inputRef.current) {
      inputRef.current.focus();
    }
  };

  return (
    <div>
      <MyInput ref={inputRef} placeholder="Enter text" />
      <button onClick={handleClick}>Focus Input</button>
    </div>
  );
}
```

This example shows `forwardRef` used to create `MyInput`, which can receive a ref from `App` and pass it down to the actual `input` DOM element, allowing `App` to focus the input.

### `memo(type, compare?)`

`memo` is a higher-order component (a function that takes a component and returns a new component) that memorizes the rendering of a functional component. If the component's props are the same as the previous render (based on a shallow comparison by default or a custom `compare` function), React skips rendering the component and reuses the last rendered result. This can offer significant performance optimizations by preventing unnecessary re-renders of components that receive unchanging props.

**Parameters**

| Name | Type | Description |
|---|---|---|
| `type` | `React$ElementType` | The functional component to memoize. `memo` works only with functional components. |
| `compare` | `?(oldProps: Props, newProps: Props) => boolean` | An optional comparison function. If provided, React will use this function to compare the `oldProps` and `newProps`. If it returns `true`, the update is skipped; if `false`, the component will re-render. If omitted, React performs a shallow comparison of all props. |

**Returns**

| Name | Type | Description |
|---|---|---|
| `elementType` | `object` | A memoized React component type. This new component behaves like the original `type` but includes the memoization optimization. |

**Example**

```javascript
import React, { memo, useState } from 'react';

const MyMemoizedComponent = memo(function MyComponent({ value }) {
  console.log('MyMemoizedComponent rendered'); // This will only log when 'value' actually changes
  return <p>Value: {value}</p>;
});

function App() {
  const [count, setCount] = useState(0);
  const [text, setText] = useState('hello');

  // MyMemoizedComponent will only re-render when its 'value' prop changes.
  // Changes to 'count' will cause App to re-render, but MyMemoizedComponent will not
  // re-render because its 'value' prop (text) has not changed.
  return (
    <div>
      <button onClick={() => setCount(count + 1)}>Increment Count: {count}</button>
      <input value={text} onChange={e => setText(e.target.value)} />
      <MyMemoizedComponent value={text} />
    </div>
  );
}
```

This example demonstrates how `memo` can prevent `MyMemoizedComponent` from re-rendering when the `count` state in its parent `App` changes, as long as `MyMemoizedComponent`'s `value` prop remains shallowly equal.

---

This section covered React's fundamental component classes, core element creation and manipulation utilities, and special built-in components for specific behaviors like performance profiling or handling loading states. Understanding these APIs is crucial for constructing robust and efficient client-side React applications. Next, explore other general-purpose functions for managing children and accessing React's version information in the [Utilities](./client-apis-utilities.md) section.