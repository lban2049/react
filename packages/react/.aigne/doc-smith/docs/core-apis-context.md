# Context

Context provides a way to pass data through the component tree without having to pass props down manually at every level. It is designed to share data that can be considered "global" for a tree of React components, such as the current authenticated user, theme, or preferred language.

When you have data that many components at different nesting levels need, you can often find yourself passing props through components that don't need the data themselves. This is commonly known as "prop drilling" and can make components less reusable and harder to refactor. Context solves this problem by allowing a parent component to make certain data available to any component in the tree below it, no matter how deep, without explicitly passing a prop.

### How Context Works

The context flow involves three main parts:
1.  **`React.createContext()`**: Creates a Context object.
2.  **`Context.Provider`**: A component that provides the context value to its descendants.
3.  **`Context.Consumer` or `useContext()`**: Components that subscribe to context changes and receive the value.

```d2
direction: down

"App": {
  label: "App"
  shape: rectangle
  style: {
    fill: "#f0f0f0"
  }

  "Provider": {
    label: "<ThemeContext.Provider value='dark'>"
    shape: rectangle
    style: {
      fill: "#c5e1a5"
    }

    "IntermediateComponent": {
      label: "IntermediateComponent\n(Doesn't need theme)"
      shape: rectangle
      style: {
        fill: "#fff9c4"
      }

      "ConsumingComponent": {
        label: "ConsumingComponent\n(Uses theme via useContext)"
        shape: rectangle
        style: {
          fill: "#b3e5fc"
        }
      }
    }
  }
}

"Provider" -> "ConsumingComponent": "Context value ('dark') passed directly" {
  style: {
    stroke-dash: 4
    animated: true
  }
}

"App"."Provider"."IntermediateComponent" -> "App"."Provider"."IntermediateComponent"."ConsumingComponent": "Standard Prop Passing (Bypassed by Context)" {
  style: {
    stroke: "#cccccc"
  }
}
```

## `createContext(defaultValue)`

This function is the entry point for creating a new context. It returns a context object that contains `Provider` and `Consumer` components.

**Parameters**

| Name | Type | Description |
|---|---|---|
| `defaultValue` | `T` | The value that a consumer will receive if it's rendered outside of a matching `Provider`. This can be useful for testing components in isolation without wrapping them. |

**Returns**

A `ReactContext` object with the following properties:

| Property | Type | Description |
|---|---|---|
| `Provider` | `React.ComponentType` | A component that allows consuming components to subscribe to context changes. |
| `Consumer` | `React.ComponentType` | A component that subscribes to context changes. Requires a function as a child (render prop). |

**Example**

Here, we create a context for the current UI theme, with `'light'` as the default value.

```javascript
import { createContext } from 'react';

// The default value is only used when a component does not have a matching
// Provider above it in the tree.
export const ThemeContext = createContext('light');
```

## `Context.Provider`

Every Context object comes with a `Provider` component that allows consuming components to subscribe to context changes.

The `Provider` component accepts a `value` prop to be passed to consuming components that are descendants of this `Provider`. All consumers that are descendants of a `Provider` will re-render whenever the `Provider`’s `value` prop changes.

**Props**

| Name | Type | Description |
|---|---|---|
| `value` | `T` | The value to be made available to all descendant consumers of this provider. |
| `children` | `React.Node` | The part of the component tree that can access the context value. |

**Example**

Wrap a component tree with the provider to make the theme value available to any component within it.

```javascript
import React, { useState } from 'react';
import { ThemeContext } from './ThemeContext';
import Toolbar from './Toolbar';

function App() {
  const [theme, setTheme] = useState('dark');

  // The value 'dark' will be passed to any descendant component that consumes ThemeContext
  return (
    <ThemeContext.Provider value={theme}>
      <Toolbar />
    </ThemeContext.Provider>
  );
}
```

## Consuming Context

There are two ways to read a context value: the modern hook-based approach and the traditional render-prop component.

### `useContext` (Recommended)

The `useContext` hook is the modern and preferred way to consume context in function components. It accepts a context object (the value returned from `React.createContext`) and returns the current context value for that context.

This makes consuming context values clean and straightforward. For a complete guide, please refer to the [useContext Hook documentation](./hooks-other.md).

**Example**

```javascript
import React, { useContext } from 'react';
import { ThemeContext } from './ThemeContext';

function ThemedButton() {
  // useContext returns the value from the nearest ThemeContext.Provider
  const theme = useContext(ThemeContext);

  return <button style={{ background: theme === 'dark' ? '#333' : '#FFF' }}>A Themed Button</button>;
}
```

### `Context.Consumer`

`Context.Consumer` is a component that subscribes to context changes. It is useful in class components or older codebases where Hooks are not available.

It requires a function as a child. This function receives the current context value and returns a React node.

**Example**

```javascript
import React from 'react';
import { ThemeContext } from './ThemeContext';

class ThemedButton extends React.Component {
  render() {
    return (
      <ThemeContext.Consumer>
        {theme => (
          <button style={{ background: theme === 'dark' ? '#333' : '#FFF' }}>
            A Themed Button (Class Component)
          </button>
        )}
      </ThemeContext.Consumer>
    );
  }
}
```

---

Context is a powerful tool for sharing state across your application. To learn more about the most common way to use it, continue to the documentation for the [`useContext` Hook](./hooks-other.md).
