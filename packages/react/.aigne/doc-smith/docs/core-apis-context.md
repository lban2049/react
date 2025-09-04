# Context

Context provides a way to pass data through the component tree without having to pass props down manually at every level. It's designed to share data that can be considered "global" for a tree of React components, such as the current authenticated user, theme, or preferred language.

While Context is a powerful feature, it should be used with care as it can make component reuse more challenging. For many cases, prop drilling or component composition are simpler and more appropriate solutions. Before using Context, consider if you can solve the problem by [lifting state up](https://react.dev/learn/sharing-state-between-components).

## Core Concepts

The Context API revolves around three main parts: `createContext`, `Provider`, and `Consumer`.

```d2
direction: down

App: {
  shape: rectangle
  label: "App Component"

  Context-Provider: {
    label: "<MyContext.Provider value={...}>"
    shape: package

    IntermediateComponent: {
      shape: rectangle
      label: "Intermediate Component\n(Props are not passed through here)"

      DeeplyNestedComponent: {
        shape: rectangle
        label: "Deeply Nested Component\n(Consumes the context value)"
      }
    }
  }
}

Context-Provider -> DeeplyNestedComponent: "Provides value directly" {
  style.stroke-dash: 4
}
```

### 1. `createContext`

This function creates a Context object. When React renders a component that subscribes to this Context object, it will read the current context value from the closest matching `Provider` above it in the tree.

The `defaultValue` argument is **only** used when a component does not have a matching Provider above it in the tree. This can be helpful for testing components in isolation without wrapping them.

**API Signature**

```javascript
const MyContext = React.createContext(defaultValue);
```

**Implementation Details**

The `createContext` function initializes a context object with several internal properties, including the `Provider` and `Consumer` components.

```javascript
// A simplified view from src/ReactContext.js

export function createContext<T>(defaultValue: T): ReactContext<T> {
  const context: ReactContext<T> = {
    $$typeof: REACT_CONTEXT_TYPE,
    _currentValue: defaultValue,
    // ... other internal fields
    Provider: null, // Will be set to the context object itself
    Consumer: null, // Will be set to a consumer object
  };

  context.Provider = context;
  context.Consumer = {
    $$typeof: REACT_CONSUMER_TYPE,
    _context: context,
  };

  return context;
}
```

### 2. `Context.Provider`

Every Context object comes with a `Provider` React component that allows consuming components to subscribe to context changes. The provider accepts a `value` prop to be passed to consuming components that are descendants of this `Provider`. All consumers that are descendants of a `Provider` will re-render whenever the `Provider`’s `value` prop changes.

```jsx
// ThemeContext.js
import { createContext } from 'react';
export const ThemeContext = createContext('light');

// App.js
import { ThemeContext } from './ThemeContext';
import Toolbar from './Toolbar';

function App() {
  return (
    // Any component inside this Provider can read its value
    <ThemeContext.Provider value="dark">
      <Toolbar />
    </ThemeContext.Provider>
  );
}
```

### 3. Consuming Context

There are two ways to consume a context value.

#### `useContext` Hook (Recommended)

The modern and preferred way to read context in a function component is the `useContext` Hook. It's more concise and easier to read than the `Context.Consumer` component.

You can learn more about it in the [Hooks documentation](./hooks-other.md).

```jsx
// ThemedButton.js
import { useContext } from 'react';
import { ThemeContext } from './ThemeContext';

function ThemedButton() {
  const theme = useContext(ThemeContext); // 'theme' will be 'dark'
  return <button className={theme}>I am a {theme} button</button>;
}
```

#### `Context.Consumer`

A `Context.Consumer` is a React component that subscribes to context changes. It requires a function as a child (a render prop). The function receives the current context `value` and returns a React node.

This approach is still supported but is less common in modern React codebases.

```jsx
import { ThemeContext } from './ThemeContext';

function ThemedButton() {
  return (
    <ThemeContext.Consumer>
      {theme => (
        <button className={theme}>I am a {theme} button</button>
      )}
    </ThemeContext.Consumer>
  );
}
```

## When to Use Context

Context is primarily used when some data needs to be accessible by many components at different nesting levels. Apply it sparingly because it makes component reuse more difficult.

If you only want to avoid passing some props through many levels, component composition is often a simpler solution than context.

Good candidates for Context:
- UI Theming (e.g., dark/light mode)
- User Authentication Data (e.g., current user object)
- Application-level configuration or settings

## Next Steps

You now have a solid understanding of how React Context works to manage global-like state within a component tree. To continue, you can explore utilities for working with component children.

<x-card data-title="Children Utilities" data-icon="lucide:list" data-href="/core-apis/children-utilities" data-cta="Read More">
  Learn how to work with the props.children opaque data structure using the React.Children API helpers.
</x-card>
