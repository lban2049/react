# Context

In a typical React application, data is passed top-down from parent to child via props. However, this can become verbose and inconvenient for certain types of props that need to be accessed by many components at different nesting levels. This is often called "prop drilling".

Context provides a way to pass data through the component tree without having to pass props down manually at every level. It's designed to share data that can be considered "global" for a tree of React components, such as the current authenticated user, theme, or preferred language.

## Creating a Context

The first step is to create a Context object using the `createContext` function. This function accepts an optional default value, which is used by a consumer when it does not have a matching Provider above it in the tree. This can be useful for testing components in isolation without wrapping them.

```javascript Creating a Context Object icon=logos:react
import { createContext } from 'react';

// The default value is 'light'
export const ThemeContext = createContext('light');
```

The `createContext` function returns a context object that contains two components: `Provider` and `Consumer`.

## Providing Context with `Context.Provider`

To make the context value available to descendant components, you need to wrap a part of your component tree with the context's `Provider` component. The `Provider` accepts a `value` prop, which will be passed to all consuming components that are descendants of this Provider.

```jsx App.js: Using the Provider icon=logos:react
import { ThemeContext } from './ThemeContext';
import Toolbar from './Toolbar';

function App() {
  // The value 'dark' will be passed down
  return (
    <ThemeContext.Provider value="dark">
      <Toolbar />
    </ThemeContext.Provider>
  );
}
```

Any component inside the `ThemeContext.Provider` can now read the value 'dark'. A component's value is determined by the `value` prop of the closest `Provider` above it in the tree.

## Consuming Context

There are two primary ways to consume a context value: the `useContext` Hook and the `Context.Consumer` component.

### `useContext` Hook (Recommended)

The `useContext` hook is the modern and simplest way to read a context value within a function component. It accepts the context object itself (the return value of `createContext`) and returns the current context value.

```jsx ThemedButton.js: Using the useContext Hook icon=logos:react
import { useContext } from 'react';
import { ThemeContext } from './ThemeContext';

function ThemedButton() {
  const theme = useContext(ThemeContext);
  return <button className={`theme-${theme}`}>Click Me</button>;
}
```

This hook makes your components cleaner and more readable. For more details, see the [Hooks documentation](./hooks-other.md).

### `Context.Consumer` Component

For class components or older codebases, you can use the `Context.Consumer` component. This component uses the "render prop" pattern, requiring a function as its child. This function receives the current context value and returns a React element.

```jsx ThemedButton.js: Using the Consumer Component icon=logos:react
import { ThemeContext } from './ThemeContext';

function ThemedButton() {
  return (
    <ThemeContext.Consumer>
      {theme => (
        <button className={`theme-${theme}`}>Click Me</button>
      )}
    </ThemeContext.Consumer>
  );
}
```

## When to Use Context

Context is an excellent tool for managing state that needs to be accessed by many components, such as:

*   **Theming**: Passing down theme information (e.g., colors, fonts).
*   **User Authentication**: Sharing the current user's data and authentication status.
*   **Application-wide settings**: Managing locale or user preferences.

However, it's important not to overuse it. If you are only passing data down one or two levels, component composition is often a simpler and more maintainable solution than context.

## Next Steps

Now that you understand how to manage global data with Context, you can explore other core React APIs or dive deeper into Hooks.

<x-cards>
  <x-card data-title="Refs" data-icon="lucide:mouse-pointer-square" data-href="/core-apis/refs">
    Learn how to access and interact with DOM nodes or React components directly.
  </x-card>
  <x-card data-title="Hooks" data-icon="lucide:anchor" data-href="/hooks">
    Explore the full range of built-in Hooks, including a more in-depth look at useContext.
  </x-card>
</x-cards>