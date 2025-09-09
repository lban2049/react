# Other Hooks

Beyond managing state, effects, and refs, React provides a set of specialized Hooks to solve other common problems. These Hooks handle tasks like accessing shared data without prop drilling, generating unique IDs for accessibility, integrating with external data sources, and improving the debugging experience for custom Hooks. This section explores these powerful utilities.

<x-cards>
  <x-card data-title="useContext" data-icon="lucide:merge">
    Access data from a parent context provider without passing props down manually at every level.
  </x-card>
  <x-card data-title="useId" data-icon="lucide:fingerprint">
    Generate unique, stable IDs that are safe for server rendering and client hydration.
  </x-card>
  <x-card data-title="useDebugValue" data-icon="lucide:bug">
    Display custom, helpful labels for your custom Hooks in React DevTools.
  </x-card>
  <x-card data-title="useSyncExternalStore" data-icon="lucide:database-zap">
    Subscribe to external data sources and stores in a way that is compatible with concurrent rendering.
  </x-card>
</x-cards>

---

## useContext

The `useContext` Hook provides a way to consume a value from a `React.Context`. It accepts a context object (the value returned from `React.createContext`) and returns the current value for that context. This allows you to pass data deep into the component tree without having to manually pass props down through every level.

For more details on creating and providing context, see the [Context](./core-apis-context.md) documentation.

**Signature**
```javascript
const value = useContext(MyContext);
```

**Parameters**

| Parameter | Type | Description |
|---|---|---|
| `MyContext` | React Context Object | The context object returned by `React.createContext` that you want to subscribe to. |

**Example**

```javascript ThemeContext Example icon=logos:react
import React, { createContext, useContext, useState } from 'react';

// 1. Create a context
const ThemeContext = createContext('light');

// 2. A component that consumes the context
function ThemedButton() {
  const theme = useContext(ThemeContext);
  const style = {
    background: theme === 'dark' ? '#282c34' : '#ffffff',
    color: theme === 'dark' ? '#ffffff' : '#282c34',
    border: '1px solid #ccc',
    padding: '8px 16px',
    cursor: 'pointer'
  };
  return <button style={style}>Current theme: {theme}</button>;
}

// 3. The parent component that provides the context
export default function App() {
  const [theme, setTheme] = useState('light');

  const toggleTheme = () => {
    setTheme(current => (current === 'light' ? 'dark' : 'light'));
  };

  return (
    <ThemeContext.Provider value={theme}>
      <button onClick={toggleTheme}>Toggle Theme</button>
      <hr style={{margin: '1em 0'}} />
      <ThemedButton />
    </ThemeContext.Provider>
  );
}
```
In this example, `ThemedButton` uses `useContext(ThemeContext)` to get the current theme value directly from the `ThemeContext.Provider` in `App`, no matter how many components are between them.

---

## useId

`useId` is a Hook for generating unique IDs that are stable on both the server and the client. This is essential for avoiding hydration mismatches in server-rendered applications. Its primary use case is for connecting related elements for accessibility, such as linking a `<label>` to an `<input>`.

**Signature**
```javascript
const uniqueId = useId();
```

**Example**

```javascript Accessible Form Field icon=logos:react
import React, { useId } from 'react';

function EmailField() {
  const id = useId();
  return (
    <div>
      <label htmlFor={id}>Email:</label>
      <input id={id} type="email" name="email" />
    </div>
  );
}

export default function NewsletterForm() {
  return (
    <form>
      <h3>Sign up for our newsletter</h3>
      <EmailField />
      <EmailField />
    </form>
  );
}
```
Each `EmailField` instance will generate a unique, stable ID. This ensures that the `htmlFor` attribute of the label correctly corresponds to the `id` of its input field, maintaining accessibility without conflicts.

---

## useDebugValue

`useDebugValue` is a developer-only Hook that lets you display a custom label for your own custom Hooks in the React DevTools. This can make debugging complex custom Hooks much easier by providing more meaningful information at a glance.

This hook has no effect in production builds.

**Signature**
```javascript
useDebugValue(value, formatFn?);
```

**Parameters**

| Parameter | Type | Description |
|---|---|---|
| `value` | `any` | The value to display in React DevTools. |
| `formatFn` | `(value) => formattedValue` | Optional. A function to format the displayed value. It's only called when the component is inspected, which can optimize performance for complex formatting. |

**Example**

```javascript Custom Hook with Debug Value icon=logos:react
import { useState, useDebugValue, useEffect } from 'react';

function useFriendStatus(friendID) {
  const [isOnline, setIsOnline] = useState(null);

  useEffect(() => {
    // In a real app, you'd subscribe to a status service
    const status = friendID % 2 === 0; // Mock status
    setIsOnline(status);
  }, [friendID]);

  // Display a helpful label in DevTools
  useDebugValue(isOnline ? 'Online' : 'Offline');

  return isOnline;
}

export default function FriendListItem({ friend }) {
  const isOnline = useFriendStatus(friend.id);
  return (
    <li style={{ color: isOnline ? 'green' : 'black' }}>
      {friend.name}
    </li>
  );
}
```
When you inspect a `FriendListItem` component in React DevTools, you will see the `FriendStatus` hook and its state labeled as either "Online" or "Offline", making it easy to see the hook's status.

---

## useSyncExternalStore

`useSyncExternalStore` is a Hook designed for subscribing to external data sources. It ensures that your component stays in sync with data managed outside of React (like a third-party state management library, a browser API, or a WebSocket connection) in a way that is compatible with concurrent rendering features. This helps prevent UI tearing—where the UI shows inconsistent state during a render.

**Signature**
```javascript
const state = useSyncExternalStore(subscribe, getSnapshot, getServerSnapshot?);
```

**Parameters**

| Parameter | Type | Description |
|---|---|---|
| `subscribe` | `(callback) => unsubscribe` | A function that subscribes a `callback` to the external store. It must return a cleanup function that handles the unsubscription. |
| `getSnapshot` | `() => snapshot` | A function that returns the current value (snapshot) of the data in the store. The returned value should be immutable. |
| `getServerSnapshot` | `() => snapshot` | Optional. A function that returns the initial data snapshot for server-side rendering (SSR) and hydration. |

**Example: Subscribing to a Browser API**

```javascript Subscribing to Network Status icon=logos:react
import { useSyncExternalStore } from 'react';

function subscribe(callback) {
  window.addEventListener('online', callback);
  window.addEventListener('offline', callback);
  return () => {
    window.removeEventListener('online', callback);
    window.removeEventListener('offline', callback);
  };
}

function getSnapshot() {
  return navigator.onLine;
}

export default function ChatIndicator() {
  const isOnline = useSyncExternalStore(subscribe, getSnapshot);
  return <h1>{isOnline ? '✅ Online' : '❌ Disconnected'}</h1>;
}
```
This component safely subscribes to the browser's network status. `useSyncExternalStore` guarantees that the component will re-render correctly when the network status changes and that the UI will remain consistent, even in concurrent mode.

---

By mastering these Hooks, you can write more efficient, accessible, and maintainable React applications. For more complex scenarios and patterns, continue to our [Advanced Guides](./advanced.md).