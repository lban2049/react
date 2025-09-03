# Other Hooks

This section covers a set of specialized Hooks that handle various tasks, from reading context and generating unique IDs to integrating with external data sources and debugging custom Hooks. While [State Hooks](./hooks-state.md) manage component data and [Effect Hooks](./hooks-effect.md) handle side effects, these Hooks provide solutions for other common needs in React applications.

---

## `useContext`

Accepts a context object (the value returned from `React.createContext`) and returns the current context value. The current context value is determined by the `value` prop of the nearest `<MyContext.Provider>` above the calling component in the tree.

When the nearest `<MyContext.Provider>` above the component updates, this Hook will trigger a re-render with the latest context `value`.

### API

```javascript
const value = useContext(SomeContext);
```

### Parameters

| Parameter | Type | Description |
|---|---|---|
| `Context` | React Context Object | The context object returned from `React.createContext`. |

### Returns

Returns the context value for the calling component.

### Example

```javascript
import React, { createContext, useContext } from 'react';

// 1. Create a context
const ThemeContext = createContext('light');

// 2. A component that uses the context
function ThemedButton() {
  const theme = useContext(ThemeContext);
  return <button>Current theme is: {theme}</button>;
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

## `useId`

`useId` is a Hook for generating unique IDs that are stable across both server and client, which is essential for preventing hydration mismatches in server-rendered applications. It is primarily used for accessibility attributes that need to connect two elements, such as a `<label>` and an `<input>`.

### API

```javascript
const uniqueId = useId();
```

### Parameters

This hook takes no parameters.

### Returns

A unique and stable string ID. The ID is prefixed with `:r` to ensure it doesn't conflict with CSS selectors.

### Example

```javascript
import { useId } from 'react';

function FormField() {
  const id = useId();
  return (
    <div>
      <label htmlFor={id}>Your Email:</label>
      <input id={id} type="email" name="email" />
    </div>
  );
}

export default function SignupForm() {
  return (
    <form>
      <p>Sign up for our newsletter:</p>
      <FormField />
      <FormField />
    </form>
  );
}
```
In the example above, each `FormField` instance will get its own unique ID, correctly associating each label with its input.

---

## `useDebugValue`

`useDebugValue` can be used to display a label for custom Hooks in React DevTools. It helps in inspecting and debugging the internal state of your custom Hooks.

**Note:** This Hook only has an effect in development mode and is ignored in production builds.

### API

```javascript
useDebugValue(value, formatFn?);
```

### Parameters

| Parameter | Type | Description |
|---|---|---|
| `value` | `any` | The value to display in React DevTools next to your custom Hook. |
| `formatFn` | `function` | (Optional) A formatting function that is only called when the DevTools are open. It receives the `value` and should return a formatted display value. This defers potentially expensive formatting operations. |

### Returns

This hook does not return anything (`void`).

### Example

```javascript
import { useState, useDebugValue } from 'react';

function useOnlineStatus() {
  const [isOnline, setIsOnline] = useState(navigator.onLine);

  // ... logic to listen to online/offline events ...

  // This label will appear in React DevTools
  useDebugValue(isOnline ? 'Online' : 'Offline');

  return isOnline;
}

export default function ChatApp() {
  const isOnline = useOnlineStatus();
  return <h1>{isOnline ? '✅ Connected' : '❌ Disconnected'}</h1>;
}
```
When you inspect the `ChatApp` component in React DevTools, you will see a `OnlineStatus` hook with the label "Online" or "Offline" next to it.

---

## `useSyncExternalStore`

`useSyncExternalStore` is a Hook for subscribing to an external data source. It is designed to be compatible with concurrent rendering features and ensures that your component re-renders correctly when the external data changes, avoiding visual tearing.

This is useful for integrating with third-party state management libraries or browser APIs that are not built with React state.

### API

```javascript
const state = useSyncExternalStore(subscribe, getSnapshot, getServerSnapshot?);
```

### Parameters

| Parameter | Type | Description |
|---|---|---|
| `subscribe` | `function` | A function that subscribes a callback to the store. It must return an `unsubscribe` function. |
| `getSnapshot` | `function` | A function that returns the current value (snapshot) of the data in the store. |
| `getServerSnapshot`| `function` | (Optional) A function that returns the initial snapshot of the data for server-side rendering (SSR). |

### Returns

The current value from the external store.

### Example

This example subscribes to the browser's `window.innerWidth` to display the current window width.

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
  return <p>Window width: {width}px</p>;
}
```

---

These utility Hooks address a range of specific needs within the React ecosystem. For more complex challenges, consider exploring the [Advanced Guides](./advanced.md) or consulting the complete [API Reference](./api-reference.md).