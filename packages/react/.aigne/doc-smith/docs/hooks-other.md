# Other Hooks

Beyond managing state, effects, and performance, React provides several specialized Hooks to solve other common problems. This guide covers a collection of Hooks for tasks like reading context, generating stable identifiers, debugging custom Hooks, and integrating with external data sources.

<x-cards data-columns="2">
  <x-card data-title="useContext" data-icon="lucide:arrow-down-to-dot">
    Access data from parent components without prop drilling.
  </x-card>
  <x-card data-title="useId" data-icon="lucide:fingerprint">
    Generate unique, stable IDs for accessibility attributes.
  </x-card>
  <x-card data-title="useDebugValue" data-icon="lucide:bug">
    Display custom labels in React DevTools for your custom Hooks.
  </x-card>
  <x-card data-title="useSyncExternalStore" data-icon="lucide:link-2">
    Subscribe to external stores in a way that supports concurrent features.
  </x-card>
</x-cards>

---

## useContext

The `useContext` Hook accepts a context object (the value returned from `React.createContext`) and returns the current value for that context. It's the primary way to consume context in function components, allowing you to avoid passing props down through many levels of the component tree.

For more information on the concept of context, see the [Context](./core-apis-context.md) guide.

**Syntax**

```javascript
const value = useContext(SomeContext);
```

**Parameters**

| Name | Type | Description |
|---|---|---|
| `Context` | `ReactContext` | The context object returned by `React.createContext`. |

**Returns**

The current context value, which is determined by the `value` prop of the nearest matching `<MyContext.Provider>` above the calling component in the tree.

**Example**

This example shows how a `ThemedButton` component can access the current theme without receiving it as a prop.

```javascript
import React, { createContext, useContext } from 'react';

// 1. Create a context
const ThemeContext = createContext('light');

// 2. A component that uses the context
function ThemedButton() {
  const theme = useContext(ThemeContext);
  return <button className={`theme-${theme}`}>A {theme} button</button>;
}

// 3. A component that provides the context
export default function App() {
  return (
    <ThemeContext.Provider value="dark">
      <ThemedButton />
    </ThemeContext.Provider>
  );
}
```

---

## useId

`useId` is a Hook for generating unique IDs that are stable across both server and client rendering. This is useful for avoiding hydration mismatches, especially for accessibility attributes like `htmlFor` and `id`.

**Syntax**

```javascript
const id = useId();
```

**Parameters**

None.

**Returns**

A unique string ID. The ID is stable for a given component across renders.

**Example**

Here, `useId` generates a matching ID to connect a label and an input field, which is essential for screen readers.

```javascript
import React, { useId } from 'react';

function EmailField() {
  const id = useId();
  return (
    <div>
      <label htmlFor={id}>Email Address</label>
      <input id={id} type="email" name="email" />
    </div>
  );
}
```

---

## useDebugValue

`useDebugValue` is a Hook that lets you display a custom label for your own custom Hooks in React DevTools. This makes it easier to inspect and debug the internal state of custom Hooks.

**Syntax**

```javascript
useDebugValue(value, format?);
```

**Parameters**

| Name | Type | Description |
|---|---|---|
| `value` | `any` | The value to display in React DevTools. |
| `formatterFn`| `function` | Optional. A function to format the displayed value. It receives the value as an argument and should return a formatted value. This is useful for avoiding expensive formatting operations unless the Hook is actually inspected. |

**Example**

In this custom Hook, `useDebugValue` provides a human-readable status in the DevTools component inspector.

```javascript
import { useState, useDebugValue } from 'react';

function useOnlineStatus() {
  const [isOnline, setIsOnline] = useState(true);

  // ... logic to check online status would go here ...

  // Display a readable label in React DevTools next to this hook
  useDebugValue(isOnline ? 'Online' : 'Offline');

  return isOnline;
}

export default function App() {
  const isOnline = useOnlineStatus();
  return <h1>{isOnline ? '✅ Online' : '❌ Disconnected'}</h1>;
}
```

When you inspect the `App` component in React DevTools, you will see a label for the `OnlineStatus` Hook showing either "Online" or "Offline".

---

## useSyncExternalStore

`useSyncExternalStore` is a Hook for reading and subscribing to an external data source in a way that is compatible with React's concurrent rendering features. It ensures that your component re-renders whenever the external data changes and prevents visual tearing during concurrent updates.

It is typically used by libraries that integrate with React, rather than in application code directly.

**Syntax**

```javascript
const snapshot = useSyncExternalStore(subscribe, getSnapshot, getServerSnapshot?);
```

**Parameters**

| Name | Type | Description |
|---|---|---|
| `subscribe` | `function` | A function that subscribes a callback to the store. It must return a function that unsubscribes. |
| `getSnapshot`| `function` | A function that returns a snapshot of the current data in the store. |
| `getServerSnapshot` | `function` | Optional. A function that returns a snapshot of the data for server-side rendering. |

**Example**

This example demonstrates subscribing to the browser's `window.innerWidth` property.

```javascript
import { useSyncExternalStore } from 'react';

function subscribe(callback) {
  window.addEventListener('resize', callback);
  return () => {
    window.removeEventListener('resize', callback);
  };
}

function getSnapshot() {
  return window.innerWidth;
}

export default function App() {
  const width = useSyncExternalStore(subscribe, getSnapshot);

  return <h1>Window width: {width}</h1>;
}
```
This component will now correctly re-render whenever the browser window is resized.

---

### Next Steps

You have now explored a variety of specialized Hooks that handle context, unique IDs, debugging, and external state. To dive deeper into more complex React features and patterns, continue to our [Advanced Guides](./advanced.md).