# Context

Context provides a way to pass data through the component tree without having to pass props down manually at every level. It's designed to share data that can be considered "global" for a tree of React components, such as the current authenticated user, theme, or preferred language.

While it can be tempting to use context for all state management, it's important to remember that it makes components less reusable. For simpler cases, consider [component composition](./core-apis-components-and-props.md) first. However, for managing state that needs to be accessed by many components at different nesting levels, Context is an effective solution.

## Core Concepts

The Context API is composed of three main parts:

1.  **`createContext`**: A function to create a context object.
2.  **`Context.Provider`**: A component that provides the context value to its descendants.
3.  **`useContext` Hook / `Context.Consumer`**: Ways for components to subscribe to and read the context value.

This diagram illustrates how a provider passes data directly to a deeply nested consumer, bypassing intermediate components.

```d2
direction: down

"App": {
  shape: rectangle

  "ThemeContext.Provider value='dark'": {
    shape: package
    grid-columns: 1

    "Layout": {
      shape: rectangle
      label: "Layout\n(Does not need theme)"
    }

    "ThemedButton": {
      shape: rectangle
      label: "ThemedButton\n(Consumes theme)"
    }

    "Layout" -> "ThemedButton": "Renders"
  }
}

"ThemeContext.Provider value='dark'" -> "ThemedButton": "Provides 'dark' value directly" {
  style.stroke-dash: 4
}
```

---

## API Reference

### `createContext(defaultValue)`

This function creates a Context object. When React renders a component that subscribes to this Context object, it will read the current context value from the closest matching `Provider` above it in the tree.

The `defaultValue` argument is **only** used when a component does not have a matching `Provider` above it in the tree. This can be helpful for testing components in isolation without wrapping them.

**Parameters**

| Name | Type | Description |
|---|---|---|
| `defaultValue` | `T` | The value the context will have if there is no provider in the tree. |

**Returns**

A context object with `Provider` and `Consumer` properties.

**Example**

```javascript
// src/ThemeContext.js
import { createContext } from 'react';

// The default value is 'light'
export const ThemeContext = createContext('light');
```

### `Context.Provider`

Every Context object comes with a `Provider` React component that allows consuming components to subscribe to context changes.

The `Provider` component accepts a `value` prop to be passed to consuming components that are descendants of this `Provider`. All consumers that are descendants of a `Provider` will re-render whenever the `Provider`’s `value` prop changes.

**Props**

| Name | Type | Description |
|---|---|---|
| `value` | `T` | The value to be passed to all consuming components deep in the tree. |
| `children` | `ReactNode` | The components that can access the context value. |

**Example**

```javascript
import React from 'react';
import { ThemeContext } from './ThemeContext';
import Toolbar from './Toolbar';

function App() {
  // Any component inside this provider can now read the 'dark' value
  return (
    <ThemeContext.Provider value="dark">
      <Toolbar />
    </ThemeContext.Provider>
  );
}
```

### Consuming Context

There are two primary ways to consume a context value.

#### `useContext(Context)`

The modern and recommended way to read a context value is by using the `useContext` Hook. It's concise and fits naturally within function components.

This hook accepts a context object (the value returned from `React.createContext`) and returns the current context value for that context.

For a detailed guide, see the [useContext Hook documentation](./hooks-other.md).

**Example**

```javascript
import React, { useContext } from 'react';
import { ThemeContext } from './ThemeContext';

function ThemedButton() {
  // The useContext hook gets the value from the nearest Provider
  const theme = useContext(ThemeContext);

  return (
    <button style={{ background: theme === 'dark' ? '#333' : '#FFF', color: theme === 'dark' ? 'white' : 'black' }}>
      The current theme is {theme}
    </button>
  );
}
```

#### `Context.Consumer`

For use in class components or in scenarios where Hooks are not available, you can use the `Consumer` component.

This requires a function as a child (a "render prop"). The function receives the current context value and returns a React node.

**Example**

```javascript
import React from 'react';
import { ThemeContext } from './ThemeContext';

class ThemedButton extends React.Component {
  render() {
    return (
      <ThemeContext.Consumer>
        {theme => (
          <button style={{ background: theme === 'dark' ? '#333' : '#FFF', color: theme === 'dark' ? 'white' : 'black' }}>
            The current theme is {theme}
          </button>
        )}
      </ThemeContext.Consumer>
    );
  }
}
```

---

## Next Steps

Now that you understand how to pass data through your application with Context, you can explore utilities for working with component children.

<x-card data-title="Children Utilities" data-icon="lucide:box" data-href="/core-apis/children-utilities" data-cta="Read More">
  Learn how to work with the props.children data structure using the React.Children API helpers.
</x-card>