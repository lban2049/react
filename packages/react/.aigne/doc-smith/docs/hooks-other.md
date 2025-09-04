# Other Hooks

Beyond managing state, effects, and performance, React provides several other specialized Hooks that address distinct needs within a component. This section covers hooks for consuming context, generating stable IDs, subscribing to external stores, and aiding in debugging.

These Hooks provide solutions for common challenges like prop drilling, accessibility, integration with non-React systems, and improving the development experience.

For an introduction to the concept of context, see the [Context](./core-apis-context.md) documentation.

---

## `useContext`

The `useContext` Hook allows a component to subscribe to React context without introducing nesting. It provides a cleaner way to consume a value from a `Context.Provider` higher up in the component tree.

### Syntax

```javascript
const value = useContext(ContextObject);
```

### Parameters

| Parameter | Type | Description |
|---|---|---|
| `ContextObject` | `ReactContext` | The context object returned by `React.createContext`. |

### Returns

The current context value for the component, determined by the `value` prop of the nearest `Context.Provider` above it in the tree.

### Example

This example demonstrates how a deeply nested `ThemedButton` can access the `theme` value without it being passed down through props.

```javascript
import React, { createContext, useContext } from 'react';

// 1. Create a context
const ThemeContext = createContext('light');

// A component that uses the hook
function ThemedButton() {
  // 3. Consume the context value
  const theme = useContext(ThemeContext);
  return <button style={{ background: theme === 'dark' ? '#333' : '#FFF', color: theme === 'dark' ? '#FFF' : '#333' }}>I am a {theme} button</button>;
}

// A middle component that doesn't need to know about the theme
function Toolbar() {
  return (
    <div>
      <ThemedButton />
    </div>
  );
}

export default function App() {
  // 2. Provide the context value
  return (
    <ThemeContext.Provider value="dark">
      <Toolbar />
    </ThemeContext.Provider>
  );
}
```

---

## `useId`

`useId` is a Hook for generating unique IDs that are stable across both server and client rendering. This is primarily useful for avoiding hydration mismatches when generating IDs for accessibility attributes like `htmlFor` and `id`.

### Syntax

```javascript
const uniqueId = useId();
```

### Parameters

None.

### Returns

A unique, stable string ID. The ID is guaranteed to be the same between the server-rendered and client-rendered output.

### Example

Here, `useId` generates a consistent ID to link a `label` and an `input` element, which is crucial for accessibility.

```javascript
import React, { useId } from 'react';

function EmailField() {
  const id = useId();
  return (
    <>
      <label htmlFor={id}>Email address</label>
      <input id={id} type="email" name="email" />
    </>
  );
}

export default function SignupForm() {
  return (
    <form>
      <EmailField />
      {/* You can render multiple instances, and each will have a unique ID */}
      <EmailField />
    </form>
  );
}
```

---

## `useSyncExternalStore`

This Hook is designed to let React components safely and efficiently subscribe to an external data source or store. It ensures that the component re-renders correctly when the external data changes and is compatible with concurrent rendering features, preventing visual tearing.

It is often used by state management libraries or when integrating with browser APIs.

### Syntax

```javascript
const snapshot = useSyncExternalStore(subscribe, getSnapshot, getServerSnapshot?);
```

### Parameters

| Parameter | Type | Description |
|---|---|---|
| `subscribe` | `(() => void) => () => void` | A function that takes a `callback` and subscribes it to the store. It must return a function that handles the cleanup/unsubscription. |
| `getSnapshot` | `() => T` | A function that returns a snapshot of the current data in the store. The component will re-render if the returned value changes. |
| `getServerSnapshot` | `() => T` | (Optional) A function that returns the initial snapshot of the data for server-side rendering (SSR). |

### Returns

The current snapshot of the data from the external store.

### Example

This example shows how to use `useSyncExternalStore` to subscribe to the browser's online status.

```javascript
import { useSyncExternalStore } from 'react';

function subscribe(callback) {
  window.addEventListener('online', callback);
  window.addEventListener('offline', callback);
  return () => {
    window.removeEventListener('online', callback);
    window.removeEventListener('offline',callback);
  };
}

function getSnapshot() {
  return navigator.onLine;
}

// The server snapshot always assumes the user is online initially.
function getServerSnapshot() {
  return true;
}

export default function OnlineStatus() {
  const isOnline = useSyncExternalStore(subscribe, getSnapshot, getServerSnapshot);

  return <h1>{isOnline ? '✅ Online' : '❌ Disconnected'}</h1>;
}
```

---

## `useDebugValue`

`useDebugValue` is a Hook that lets you display a custom label for your custom Hooks in React DevTools. It is intended for debugging and has no effect in production builds.

### Syntax

```javascript
useDebugValue(value, formatFn?);
```

### Parameters

| Parameter | Type | Description |
|---|---|---|
| `value` | `any` | The value to display in React DevTools next to your custom hook's name. |
| `formatFn` | `(value) => formattedValue` | (Optional) A function to format the displayed value. This function is only called when the component is inspected in DevTools, allowing you to avoid potentially expensive formatting operations otherwise. |

### Example

In this example, we create a custom `useOnlineStatus` Hook and use `useDebugValue` to display a user-friendly status string in DevTools.

```javascript
import React, { useState, useEffect, useDebugValue } from 'react';

// Custom Hook
function useOnlineStatus() {
  const [isOnline, setIsOnline] = useState(true);

  useEffect(() => {
    function handleOnline() { setIsOnline(true); }
    function handleOffline() { setIsOnline(false); }
    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);
    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, []);

  // Display a custom label in React DevTools
  useDebugValue(isOnline ? 'Online' : 'Offline');

  return isOnline;
}

export default function App() {
  const isOnline = useOnlineStatus();
  return <p>User is: {isOnline ? 'Online' : 'Offline'}</p>;
}

```
When you inspect the `App` component in React DevTools, you will see `OnlineStatus: "Online"` in the Hooks tree, making it easy to see the current state of your custom hook at a glance.

---

This section has covered a variety of Hooks that handle specific concerns from context to debugging. With a solid understanding of all the built-in Hooks, you are well-equipped to build complex and efficient React applications.

To explore more complex React features, patterns, and environments, continue to the [Advanced Guides](./advanced.md).