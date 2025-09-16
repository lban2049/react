# Top-Level APIs

This section provides detailed documentation for the top-level functions available in the `react` package. These APIs are the fundamental building blocks for creating components, managing context, and performing advanced optimizations. While modern workflows with JSX and Hooks might abstract some of these away, understanding them is key to mastering React.

For APIs related to state and lifecycle in function components, please see the [Hooks documentation](./api-reference-hooks.md). For built-in components like `<Fragment>` and `<Suspense>`, refer to the [Components & Classes reference](./api-reference-components.md).

---

## createElement()

Creates and returns a new React element of the given type. This is the fundamental function that JSX compiles down to in the classic JSX transform. You typically won't invoke `createElement()` directly if you are using JSX.

### Parameters
<x-field data-name="type" data-type="string | function | class" data-required="true" data-desc="The element type. Can be a tag name string (e.g., 'div', 'span'), a React component type (a class or a function), or a React fragment type."></x-field>
<x-field data-name="config" data-type="object" data-required="false" data-desc="An object that contains props for the element. It can also contain special properties like 'key' and 'ref'."></x-field>
<x-field data-name="...children" data-type="ReactNode" data-required="false" data-desc="A variable number of child arguments. These can be other React elements, strings, numbers, or arrays of nodes."></x-field>

### Returns
<x-field data-name="ReactElement" data-type="object" data-desc="A React Element object, which is a lightweight description of what to render."></x-field>

### Example

```javascript Example icon=logos:javascript
import React from 'react';

// JSX syntax
const elementWithJsx = <h1 className="greeting">Hello, world!</h1>;

// The equivalent call using React.createElement
const elementWithoutJsx = React.createElement(
  'h1',
  { className: 'greeting' },
  'Hello, world!'
);
```

---

## createContext()

Creates a Context object. When React renders a component that subscribes to this Context object, it will read the current context value from the closest matching `Provider` up in the tree.

### Parameters
<x-field data-name="defaultValue" data-type="any" data-required="true" data-desc="The value that a consumer gets when there is no matching Provider in the tree above it. This can be helpful for testing components in isolation."></x-field>

### Returns
<x-field data-name="Context" data-type="object" data-desc="A Context object with two properties: `Provider` and `Consumer`. Most modern apps will use the `useContext` Hook instead of the `Consumer` component.">
  <x-field data-name="Provider" data-type="React.Component" data-desc="A component that allows consumers to subscribe to context changes."></x-field>
  <x-field data-name="Consumer" data-type="React.Component" data-desc="A component that subscribes to context changes. Requires a function as a child."></x-field>
</x-field>

### Example

```javascript ThemeContext.js icon=logos:javascript
import { createContext } from 'react';

export const ThemeContext = createContext('light');
```

```javascript App.js icon=logos:javascript
import { ThemeContext } from './ThemeContext';
import ThemedButton from './ThemedButton';

function App() {
  return (
    <ThemeContext.Provider value="dark">
      <ThemedButton />
    </ThemeContext.Provider>
  );
}
```

```javascript ThemedButton.js icon=logos:javascript
import { useContext } from 'react';
import { ThemeContext } from './ThemeContext';

function ThemedButton() {
  const theme = useContext(ThemeContext);
  return <button className={`theme-${theme}`}>I am a {theme} button</button>;
}
```

---

## forwardRef()

`forwardRef` creates a React component that forwards the `ref` attribute it receives to another component below in the tree. This is particularly useful for exposing the DOM node of a child component to its parent for imperative actions, like managing focus.

### Parameters
<x-field data-name="render" data-type="function" data-required="true" data-desc="A render function that receives `props` as its first argument and the `ref` as its second argument. This function should return a React node."></x-field>

### Returns
<x-field data-name="ReactComponent" data-type="object" data-desc="A new React component type that can be rendered."></x-field>

### Example

```javascript FancyButton.js icon=logos:javascript
import React, { forwardRef } from 'react';

const FancyButton = forwardRef((props, ref) => (
  <button ref={ref} className="FancyButton">
    {props.children}
  </button>
));

export default FancyButton;
```

```javascript App.js icon=logos:javascript
import React, { useRef, useEffect } from 'react';
import FancyButton from './FancyButton';

function App() {
  const buttonRef = useRef(null);

  useEffect(() => {
    // The ref gives us direct access to the <button> DOM element
    if (buttonRef.current) {
      buttonRef.current.focus();
    }
  }, []);

  return (
    <FancyButton ref={buttonRef}>
      Click me!
    </FancyButton>
  );
}
```

---

## lazy()

`lazy()` lets you define a component that is loaded dynamically (code-splitting). This helps reduce the initial bundle size of your application. `lazy` components should be rendered inside a `Suspense` component, which allows you to specify a loading indicator.

### Parameters
<x-field data-name="factory" data-type="function" data-required="true" data-desc="A function that must call a dynamic `import()`. This function must return a Promise which resolves to a module with a `default` export containing a React component."></x-field>

### Returns
<x-field data-name="LazyComponent" data-type="object" data-desc="A special React component type that you can render in your component tree."></x-field>

### Example

```javascript App.js icon=logos:javascript
import React, { Suspense, lazy } from 'react';

const OtherComponent = lazy(() => import('./OtherComponent'));

function App() {
  return (
    <div>
      <h1>My App</h1>
      <Suspense fallback={<div>Loading...</div>}>
        <OtherComponent />
      </Suspense>
    </div>
  );
}
```

---

## memo()

`memo()` is a higher-order component that memoizes a rendered component. It's React's way of preventing re-renders for function components if their props are shallowly equal. It's a performance optimization.

### Parameters
<x-field data-name="Component" data-type="function" data-required="true" data-desc="The function component to be memoized."></x-field>
<x-field data-name="areEqual" data-type="function" data-required="false" data-desc="An optional custom comparison function. It receives the old props and new props. If it returns true, the update is skipped."></x-field>

### Returns
<x-field data-name="MemoizedComponent" data-type="object" data-desc="A new, memoized React component type."></x-field>

### Example

```javascript MyComponent.js icon=logos:javascript
import React, { memo } from 'react';

const MyComponent = memo(function MyComponent(props) {
  /* renders only when props change */
  console.log('Rendering MyComponent');
  return <div>{props.text}</div>;
});

export default MyComponent;
```

---

## Other Top-Level APIs

<x-cards>
<x-card data-title="cloneElement()" data-icon="lucide:copy">
Clones and returns a new React element using an element as the starting point. `config` can contain new props. The resulting element will have the original element's props with the new props merged in.
</x-card>
<x-card data-title="createRef()" data-icon="lucide:mouse-pointer-square">
Creates a ref object. It's typically used in class components to get a reference to a DOM element or a component instance. In function components, the `useRef` Hook is preferred.
</x-card>
<x-card data-title="isValidElement()" data-icon="lucide:check-square">
Verifies if an object is a React element. Returns `true` if the object is a valid React element, otherwise `false`. Useful for validating `props.children`.
</x-card>
</x-cards>

### Next Steps

Now that you understand these top-level APIs, you may want to learn how to work with the `props.children` data structure in more detail. Continue to the next section to learn about the `React.Children` utilities.

<x-card data-title="Next: React.Children" data-icon="lucide:arrow-right" data-href="/api-reference/children" data-cta="Read More">
Explore utilities for mapping, counting, and manipulating child elements.
</x-card>