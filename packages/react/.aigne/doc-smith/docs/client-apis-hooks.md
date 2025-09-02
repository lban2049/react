# Hooks Reference

This section provides detailed documentation for all standard and experimental React Hooks available for building interactive client-side applications. These hooks are essential for managing state, handling side effects, accessing context, and optimizing performance within your React components.

For a broader understanding of other client-side APIs, refer to the [Client-side APIs](./client-apis.md) section.

## Standard Hooks

### useState

The `useState` hook allows you to add state to function components. When the state changes, the component re-renders.

**Signature**

| Parameter | Type | Description |
|---|---|---|
| `initialState` | `(() => S) \| S` | The initial state value. It can be a value directly or a function that returns the initial value (for lazy initialization). |

**Returns**

| Name | Type | Description |
|---|---|---|
| `[state, setState]` | `[S, Dispatch<BasicStateAction<S>>]` | An array containing the current state value (`S`) and a state setter function (`setState`) to update it. |

**Example**

```javascript
import React, { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>Click me</button>
    </div>
  );
}

// To use this component:
// <Counter />
```

This example initializes a `count` state variable to `0` and provides a button to increment it, demonstrating how to declare and update state within a function component.

### useEffect

The `useEffect` hook allows you to perform side effects in function components, such as data fetching, subscriptions, or manually changing the DOM. Effects run after every render, but you can control when they re-run using dependencies.

**Signature**

| Parameter | Type | Description |
|---|---|---|
| `create` | `() => (() => void) \| void` | The effect function. It can optionally return a cleanup function, which runs before the component unmounts or before the effect re-runs. |
| `deps` | `Array<mixed> \| void \| null` | An optional array of dependencies. The effect will only re-run if any value in this array changes. If omitted or an empty array `[]` is provided, the effect runs once after the initial render. |

**Returns**

| Name | Type | Description |
|---|---|---|
| `void` | `void` | This hook does not return a value. |

**Example**

```javascript
import React, { useState, useEffect } from 'react';

function DocumentTitleUpdater() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    // Update the document title after every render
    document.title = `You clicked ${count} times`;

    // Optional cleanup function
    return () => {
      console.log('Cleanup for count:', count);
    };
  }, [count]); // Re-run effect only if count changes

  return (
    <div>
      <p>Current count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}

// To use this component:
// <DocumentTitleUpdater />
```

This example updates the document title to reflect the `count` state. The `useEffect` hook with `[count]` as its dependency array ensures the title updates only when `count` changes, and the cleanup function logs before re-running or unmounting.

### useContext

The `useContext` hook allows you to subscribe to React Context directly from a function component, making it easier to share values like themes or user authentication status throughout your component tree without prop drilling.

**Signature**

| Parameter | Type | Description |
|---|---|---|
| `Context` | `ReactContext<T>` | The Context object created by `React.createContext()`. |

**Returns**

| Name | Type | Description |
|---|---|---|
| `value` | `T` | The current context value for the nearest `Context.Provider` above the calling component in the tree. |

**Example**

```javascript
import React, { createContext, useContext } from 'react';

// 1. Create a Context
const ThemeContext = createContext('light');

// 2. Component that consumes the Context
function ThemedButton() {
  const theme = useContext(ThemeContext);
  return <button style={{ background: theme === 'dark' ? 'black' : 'white', color: theme === 'dark' ? 'white' : 'black' }}>
    My {theme} Button
  </button>;
}

// 3. Component that provides the Context
function App() {
  return (
    <ThemeContext.Provider value="dark">
      <ThemedButton />
    </ThemeContext.Provider>
  );
}

// To use this component:
// <App />
```

This example demonstrates how `ThemeContext` is created, `ThemedButton` consumes the `theme` value using `useContext`, and `App` provides the `dark` theme value to its children.

### useReducer

The `useReducer` hook provides an alternative to `useState` for managing more complex state logic that involves multiple sub-values or when the next state depends on the previous one. It is often preferred over `useState` when state transitions are complex.

**Signature**

| Parameter | Type | Description |
|---|---|---|
| `reducer` | `(S, A) => S` | A pure function that takes the current state and an action, and returns the new state. |
| `initialArg` | `I` | The initial value that will be passed to the `init` function (if provided) or used directly as the initial state. |
| `init` | `(I) => S` | An optional initializer function that computes the initial state from `initialArg`. This allows for lazy initialization. |

**Returns**

| Name | Type | Description |
|---|---|---|
| `[state, dispatch]` | `[S, Dispatch<A>]` | An array containing the current state value (`S`) and a `dispatch` function to send actions to the reducer. |

**Example**

```javascript
import React, { useReducer } from 'react';

const initialState = { count: 0 };

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 };
    case 'decrement':
      return { count: state.count - 1 };
    case 'reset':
      return initialState;
    default:
      throw new Error();
  }
}

function CounterWithReducer() {
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <>
      Count: {state.count}
      <button onClick={() => dispatch({ type: 'increment' })}>+</button>
      <button onClick={() => dispatch({ type: 'decrement' })}>-</button>
      <button onClick={() => dispatch({ type: 'reset' })}>Reset</button>
    </>
  );
}

// To use this component:
// <CounterWithReducer />
```

This example uses `useReducer` to manage a `count` state. The `reducer` function defines how the state changes based on 'increment', 'decrement', and 'reset' actions, and `dispatch` is used to send these actions.

### useRef

The `useRef` hook allows you to create a mutable ref object whose `.current` property can hold a mutable value across renders without causing re-renders when it changes. It is commonly used to access DOM elements or to store any mutable value that doesn't trigger a re-render.

**Signature**

| Parameter | Type | Description |
|---|---|---|
| `initialValue` | `T` | The initial value to be assigned to the ref's `.current` property. |

**Returns**

| Name | Type | Description |
|---|---|---|
| `refObject` | `{current: T}` | A plain JavaScript object with a single property called `current`, initialized to the `initialValue`. |

**Example**

```javascript
import React, { useRef } from 'react';

function TextInputWithFocusButton() {
  const inputEl = useRef(null);

  const onButtonClick = () => {
    // `current` points to the mounted text input element
    inputEl.current.focus();
  };

  return (
    <>
      <input ref={inputEl} type="text" />
      <button onClick={onButtonClick}>Focus the input</button>
    </>
  );
}

// To use this component:
// <TextInputWithFocusButton />
```

This example uses `useRef` to create a ref `inputEl` that is attached to an `<input>` element. Clicking the button uses `inputEl.current.focus()` to programmatically focus the input field.

### useCallback

The `useCallback` hook memoizes a callback function. It returns a memoized version of the callback that only changes if one of the dependencies has changed. This is useful for preventing unnecessary re-renders in child components that rely on reference equality to prevent re-rendering.

**Signature**

| Parameter | Type | Description |
|---|---|---|
| `callback` | `T` | The function to be memoized. |
| `deps` | `Array<mixed> \| void \| null` | An array of dependencies. The `callback` will only be re-memoized if any value in this array changes. If an empty array `[]` is provided, the callback is memoized once and never changes. |

**Returns**

| Name | Type | Description |
|---|---|---|
| `memoizedCallback` | `T` | The memoized version of the `callback` function. |

**Example**

```javascript
import React, { useState, useCallback } from 'react';

function ParentComponent() {
  const [count, setCount] = useState(0);

  const handleClick = useCallback(() => {
    setCount(prevCount => prevCount + 1);
  }, []); // Empty dependency array means the function is memoized once

  return (
    <div>
      <p>Count: {count}</p>
      <ChildComponent onClick={handleClick} />
    </div>
  );
}

function ChildComponent({ onClick }) {
  console.log('ChildComponent rendered');
  return <button onClick={onClick}>Increment from Child</button>;
}

// To use this component:
// <ParentComponent />
```

In this example, `handleClick` is memoized using `useCallback` with an empty dependency array. This ensures that `handleClick` always refers to the same function instance, preventing `ChildComponent` from re-rendering unnecessarily if `ParentComponent` re-renders for other reasons.

### useMemo

The `useMemo` hook memoizes a computed value. It only re-computes the memoized value when one of the dependencies has changed. This optimization helps to avoid expensive calculations on every render.

**Signature**

| Parameter | Type | Description |
|---|---|---|
| `create` | `() => T` | A function that computes the value to be memoized. |
| `deps` | `Array<mixed> \| void \| null` | An array of dependencies. The `create` function will only be re-executed if any value in this array changes. If an empty array `[]` is provided, the value is computed once. |

**Returns**

| Name | Type | Description |
|---|---|---|
| `memoizedValue` | `T` | The memoized value resulting from the `create` function. |

**Example**

```javascript
import React, { useState, useMemo } from 'react';

function FibonacciCalculator({ n }) {
  const [inputN, setInputN] = useState(n);

  // An expensive calculation
  const calculateFibonacci = (num) => {
    console.log('Calculating Fibonacci...');
    if (num <= 1) return num;
    return calculateFibonacci(num - 1) + calculateFibonacci(num - 2);
  };

  // Memoize the result of calculateFibonacci
  const memoizedFib = useMemo(() => calculateFibonacci(inputN), [inputN]);

  return (
    <div>
      <input
        type="number"
        value={inputN}
        onChange={(e) => setInputN(parseInt(e.target.value, 10))}
      />
      <p>Fibonacci({inputN}): {memoizedFib}</p>
    </div>
  );
}

// To use this component:
// <FibonacciCalculator n={10} />
```

This example uses `useMemo` to memoize the result of `calculateFibonacci`. The function `calculateFibonacci` will only run when `inputN` changes, preventing re-calculation if the component re-renders for other reasons.

### useImperativeHandle

The `useImperativeHandle` hook customizes the instance value that is exposed to parent components when using `ref` with `forwardRef`. It's generally used when you need to expose a limited set of methods to a parent component rather than the full DOM element.

**Signature**

| Parameter | Type | Description |
|---|---|---|
| `ref` | `{current: T \| null} \| ((inst: T \| null) => mixed) \| null \| void` | The `ref` object passed from `forwardRef`. |
| `create` | `() => T` | A function that returns the value you want to expose through the ref. |
| `deps` | `Array<mixed> \| void \| null` | An optional array of dependencies. The `create` function will only be re-executed if any value in this array changes. |

**Returns**

| Name | Type | Description |
|---|---|---|
| `void` | `void` | This hook does not return a value. |

**Example**

```javascript
import React, { useRef, useImperativeHandle, forwardRef } from 'react';

const MyInput = forwardRef((props, ref) => {
  const inputRef = useRef();

  useImperativeHandle(ref, () => ({
    focus: () => {
      inputRef.current.focus();
    },
    setValue: (val) => {
      inputRef.current.value = val;
    }
  }));

  return <input type="text" ref={inputRef} />;
});

function ParentComponent() {
  const myInputRef = useRef();

  const handleFocus = () => {
    myInputRef.current.focus();
  };

  const handleSetValue = () => {
    myInputRef.current.setValue('Hello Imperative Handle');
  };

  return (
    <div>
      <MyInput ref={myInputRef} />
      <button onClick={handleFocus}>Focus Input</button>
      <button onClick={handleSetValue}>Set Value</button>
    </div>
  );
}

// To use this component:
// <ParentComponent />
```

This example shows `MyInput` using `useImperativeHandle` to expose `focus` and `setValue` methods to its parent via `myInputRef`, allowing the parent to control the input element more granularly.

### useLayoutEffect

The `useLayoutEffect` hook is similar to `useEffect`, but it fires synchronously after all DOM mutations, before the browser has a chance to paint. This is useful for reading layout from the DOM and synchronously re-rendering with the new layout, preventing visual inconsistencies (e.g., measuring the size of an element).

**Signature**

| Parameter | Type | Description |
|---|---|---|
| `create` | `() => (() => void) \| void` | The effect function. It can optionally return a cleanup function. |
| `deps` | `Array<mixed> \| void \| null` | An optional array of dependencies. The effect will only re-run if any value in this array changes. |

**Returns**

| Name | Type | Description |
|---|---|---|
| `void` | `void` | This hook does not return a value. |

**Example**

```javascript
import React, { useState, useLayoutEffect, useRef } from 'react';

function Tooltip({ children, text }) {
  const [tooltipPosition, setTooltipPosition] = useState({});
  const buttonRef = useRef(null);

  useLayoutEffect(() => {
    if (buttonRef.current) {
      const rect = buttonRef.current.getBoundingClientRect();
      setTooltipPosition({
        top: rect.bottom + 5,
        left: rect.left,
      });
    }
  }, [children]); // Recalculate if children change

  return (
    <div style={{ position: 'relative', display: 'inline-block' }}>
      <button ref={buttonRef}>{children}</button>
      <div
        style={{
          position: 'absolute',
          background: 'black',
          color: 'white',
          padding: '5px',
          borderRadius: '3px',
          ...tooltipPosition,
        }}
      >
        {text}
      </div>
    </div>
  );
}

// To use this component:
// <Tooltip text="Hello World">Hover me</Tooltip>
```

This example uses `useLayoutEffect` to measure the position of a button immediately after DOM updates and then synchronously updates the tooltip's position, ensuring it appears correctly without flicker.

### useDebugValue

The `useDebugValue` hook is a development-only hook that can be used to display a custom label for custom hooks in React DevTools. This helps in inspecting custom hook values more easily during development.

**Signature**

| Parameter | Type | Description |
|---|---|---|
| `value` | `T` | The value that you want to display in the DevTools. |
| `formatterFn` | `?(value: T) => mixed` | An optional formatter function. If provided, the formatter function will be called with the `value` to format it before displaying. This can defer expensive formatting until the hook is inspected. |

**Returns**

| Name | Type | Description |
|---|---|---|
| `void` | `void` | This hook does not return a value. |

**Example**

```javascript
import React, { useState, useDebugValue } from 'react';

function useOnlineStatus() {
  const [isOnline, setIsOnline] = useState(true);

  useDebugValue(isOnline ? 'Online' : 'Offline');

  // ... (logic to update isOnline based on network status)

  return isOnline;
}

function NetworkStatusDisplay() {
  const online = useOnlineStatus();

  return <p>You are: {online ? 'Online' : 'Offline'}</p>;
}

// To use this component:
// <NetworkStatusDisplay />
```

When inspecting `NetworkStatusDisplay` in React DevTools, `useDebugValue` will display "Online" or "Offline" next to the `useOnlineStatus` hook, providing immediate insight into its state.

### useTransition

The `useTransition` hook returns a state value indicating whether a transition is pending and a `startTransition` function to mark UI updates as transitions. Transitions are non-urgent updates that can be interrupted by more urgent updates.

**Signature**

This hook takes no parameters.

**Returns**

| Name | Type | Description |
|---|---|---|
| `[isPending, startTransition]` | `[boolean, (callback: () => void, options?: StartTransitionOptions) => void]` | An array where `isPending` is a boolean indicating if a transition is active, and `startTransition` is a function to wrap non-urgent state updates. |

**Example**

```javascript
import React, { useState, useTransition } from 'react';

function SearchBar() {
  const [inputValue, setInputValue] = useState('');
  const [displayValue, setDisplayValue] = useState('');
  const [isPending, startTransition] = useTransition();

  const handleChange = (e) => {
    setInputValue(e.target.value);

    // Mark this update as a transition
    startTransition(() => {
      setDisplayValue(e.target.value);
    });
  };

  return (
    <div>
      <input type="text" value={inputValue} onChange={handleChange} />
      {isPending && <p>Loading...</p>}
      <p>Search result for: {displayValue}</p>
    </div>
  );
}

// To use this component:
// <SearchBar />
```

This example uses `useTransition` to mark the `setDisplayValue` update as non-urgent. The "Loading..." message appears when `isPending` is `true`, indicating that the transition is in progress, allowing the input to remain responsive.

### useDeferredValue

The `useDeferredValue` hook defers updating a value, allowing you to prioritize more urgent updates (like typing into an input) while displaying an older, "stale" version of another part of the UI. This is useful for UI elements that respond to a value but are expensive to re-render.

**Signature**

| Parameter | Type | Description |
|---|---|---|
| `value` | `T` | The value that you want to defer. |
| `initialValue` | `T` | An optional initial value to use during the deferral period. |

**Returns**

| Name | Type | Description |
|---|---|---|
| `deferredValue` | `T` | The deferred version of the `value`. This value will "lag behind" the primary `value` by a configurable amount, allowing the UI to remain responsive during heavy updates. |

**Example**

```javascript
import React, { useState, useDeferredValue } from 'react';

function SearchResults({ query }) {
  // Simulate an expensive filtering operation
  const filteredItems = useMemo(() => {
    console.log('Filtering items for:', query);
    // This would be your actual filtering logic
    return Array(1000).fill(0).map((_, i) => `Item ${i}: ${query}`);
  }, [query]);
  return (
    <div>
      {filteredItems.map((item, index) => (
        <div key={index}>{item}</div>
      ))}
    </div>
  );
}

function DeferredSearchInput() {
  const [query, setQuery] = useState('');
  const deferredQuery = useDeferredValue(query);

  return (
    <div>
      <input value={query} onChange={e => setQuery(e.target.value)} placeholder="Search..." />
      <SearchResults query={deferredQuery} />
    </div>
  );
}

// To use this component:
// <DeferredSearchInput />
```

This example defers the `query` used by `SearchResults`. Typing into the input updates `query` immediately, keeping the input responsive, while `deferredQuery` (and thus `SearchResults`) updates only after a short delay or when the system is idle, preventing UI jank from expensive filtering.

### useId

The `useId` hook generates a unique, stable ID that is guaranteed to be unique across both the server and the client. This is useful for associating labels with input fields, or for generating unique IDs for accessibility attributes when hydrating HTML.

**Signature**

This hook takes no parameters.

**Returns**

| Name | Type | Description |
|---|---|---|
| `id` | `string` | A unique string ID. |

**Example**

```javascript
import React, { useId } from 'react';

function PasswordField() {
  const passwordHintId = useId();
  return (
    <div>
      <label htmlFor="password">Password:</label>
      <input id="password" type="password" aria-describedby={passwordHintId} />
      <p id={passwordHintId}>Password must be at least 8 characters long.</p>
    </div>
  );
}

// To use this component:
// <PasswordField />
```

This example uses `useId` to generate a unique ID for the password hint, which is then used to associate the hint with the input field via `aria-describedby` for accessibility purposes.

### useSyncExternalStore

The `useSyncExternalStore` hook allows React components to subscribe to external stores. It ensures that reads from the store are consistent and that updates are scheduled with React's concurrency features, making it suitable for integrating with libraries that manage state outside of React's own state system (e.g., Redux, RxJS).

**Signature**

| Parameter | Type | Description |
|---|---|---|
| `subscribe` | `(() => void) => () => void` | A function that takes a callback `() => void` and subscribes it to the store. It must return an `unsubscribe` function that cleans up the subscription. |
| `getSnapshot` | `() => T` | A function that reads the current value from the store. This function must be pure. |
| `getServerSnapshot` | `() => T` | An optional function used during server-side rendering to provide the initial snapshot of the store. If not provided, the client will render empty during hydration. |

**Returns**

| Name | Type | Description |
|---|---|---|
| `value` | `T` | The current value retrieved from the external store. |

**Example**

```javascript
import React, { useSyncExternalStore, useEffect } from 'react';

// A simple external store
const myStore = {
  _value: 0,
  _listeners: new Set(),
  getValue() {
    return this._value;
  },
  setValue(newValue) {
    this._value = newValue;
    this._listeners.forEach(listener => listener());
  },
  subscribe(listener) {
    this._listeners.add(listener);
    return () => this._listeners.delete(listener);
  },
};

function ExternalStoreComponent() {
  const count = useSyncExternalStore(
    myStore.subscribe,
    myStore.getValue,
    myStore.getValue // Use getValue for server snapshot in this simple case
  );

  useEffect(() => {
    // Simulate external updates
    const interval = setInterval(() => {
      myStore.setValue(myStore.getValue() + 1);
    }, 1000);
    return () => clearInterval(interval);
  }, []);

  return <p>External Store Count: {count}</p>;
}

// To use this component:
// <ExternalStoreComponent />
```

This example integrates a simple `myStore` with React using `useSyncExternalStore`. The component subscribes to `myStore` changes, re-renders when the value updates, and displays the current count from the external store.

### use

The `use` hook allows you to read the value of a resource, such as a Promise or a Context. When used with a Promise, it integrates with Suspense, pausing rendering until the Promise resolves. When used with Context, it provides a more concise way to read context values compared to `useContext`.

**Signature**

| Parameter | Type | Description |
|---|---|---|
| `usable` | `Usable<T>` | A resource to be read. This can be a `Promise<T>` or a `ReactContext<T>`. |

**Returns**

| Name | Type | Description |
|---|---|---|
| `value` | `T` | The resolved value of the `usable` resource. |

**Example (with Context)**

```javascript
import React, { createContext, use } from 'react';

const UserContext = createContext({ name: 'Guest' });

function UserDisplay() {
  const user = use(UserContext); // Read context directly
  return <p>Current User: {user.name}</p>;
}

function AppWithUserContext() {
  return (
    <UserContext.Provider value={{ name: 'Alice' }}>
      <UserDisplay />
    </UserContext.Provider>
  );
}

// To use this component:
// <AppWithUserContext />
```

This example shows `UserDisplay` directly reading the `UserContext` value using `use(UserContext)`, making context consumption more streamlined.

**Example (with Promise and Suspense - Conceptual)**

```javascript
import React, { Suspense, use } from 'react';

const fetchData = () => {
  return new Promise(resolve => {
    setTimeout(() => resolve('Data loaded!'), 2000);
  });
};

const dataPromise = fetchData(); // This promise should be created outside render or memoized

function DataComponent() {
  const data = use(dataPromise); // Suspends until dataPromise resolves
  return <p>{data}</p>;
}

function AppWithSuspense() {
  return (
    <Suspense fallback={<div>Loading data...</div>}>
      <DataComponent />
    </Suspense>
  );
}

// To use this component:
// <AppWithSuspense />
```

This conceptual example demonstrates how `use` can read a Promise. When `DataComponent` renders, if `dataPromise` is not resolved, React Suspends and displays the `fallback` from the nearest `Suspense` boundary until the promise settles.

### useOptimistic

The `useOptimistic` hook allows you to update the UI optimistically, assuming an action will succeed before the actual asynchronous operation completes. This provides immediate feedback to the user, improving perceived performance. If the action later fails, the UI can revert to its true state.

**Signature**

| Parameter | Type | Description |
|---|---|---|
| `passthrough` | `S` | The initial optimistic state, which is typically the current state before an optimistic update. |
| `reducer` | `?(S, A) => S` | An optional reducer function. If provided, it takes the current state and an action and returns the new optimistic state. If not provided, the action itself becomes the new optimistic state. |

**Returns**

| Name | Type | Description |
|---|---|---|
| `[optimisticState, addOptimistic]` | `[S, (A) => void]` | An array containing the current optimistic state (`S`) and an `addOptimistic` function to dispatch optimistic updates. |

**Example**

```javascript
import React, { useState, useOptimistic } from 'react';

function MessageList() {
  const [messages, setMessages] = useState([]);
  const [optimisticMessages, addOptimisticMessage] = useOptimistic(
    messages,
    (currentMessages, newMessage) => [...currentMessages, newMessage]
  );

  async function sendMessage(text) {
    const newMessage = { id: Date.now(), text, sending: true };
    addOptimisticMessage(newMessage); // Optimistically add message

    try {
      // Simulate API call
      await new Promise(resolve => setTimeout(resolve, 1000));
      setMessages(prev => [...prev, { ...newMessage, sending: false }]);
    } catch (error) {
      console.error('Failed to send message:', error);
      // Revert optimistic update if necessary (e.g., filter out the optimistic message)
      setMessages(messages); // Revert to original messages
    }
  }

  return (
    <div>
      <ul>
        {optimisticMessages.map(msg => (
          <li key={msg.id} style={{ opacity: msg.sending ? 0.5 : 1 }}>
            {msg.text} {msg.sending ? '(Sending...)' : ''}
          </li>
        ))}
      </ul>
      <button onClick={() => sendMessage(`Hello from ${Math.random().toFixed(2)}`)}>Send Message</button>
    </div>
  );
}

// To use this component:
// <MessageList />
```

This example uses `useOptimistic` to immediately display a new message as "Sending..." while the actual network request is in progress. Once the request completes, the message is finalized (or potentially reverted if there's an error).

### useActionState

The `useActionState` hook allows you to manage state updates triggered by form submissions or other actions that might involve asynchronous operations. It provides the current state, a function to dispatch the action, and a boolean indicating if the action is pending, making it ideal for form handling with server actions.

**Signature**

| Parameter | Type | Description |
|---|---|---|
| `action` | `(Awaited<S>, P) => S` | The action function. It takes the current state and a payload (`P`), and returns the new state (`S`). This function can be synchronous or asynchronous. |
| `initialState` | `Awaited<S>` | The initial state for the action. |
| `permalink` | `string` | An optional permalink for the action, used for tracking or revalidation. |

**Returns**

| Name | Type | Description |
|---|---|---|
| `[state, dispatch, isPending]` | `[Awaited<S>, (P) => void, boolean]` | An array containing: `state` (the current state), `dispatch` (a function to trigger the action with a payload), and `isPending` (a boolean indicating if the action is currently running). |

**Example**

```javascript
import React, { useActionState } from 'react';

async function createUser(prevState, formData) {
  const name = formData.get('name');
  const email = formData.get('email');
  console.log(`Creating user: ${name}, ${email}`);

  // Simulate API call
  await new Promise(resolve => setTimeout(resolve, 1500));

  if (!name || !email) {
    return { message: 'Name and Email are required.', success: false };
  }

  return { message: `User ${name} created successfully!`, success: true };
}

function UserForm() {
  const [state, formAction, isPending] = useActionState(createUser, { message: '', success: false });

  return (
    <form action={formAction}>
      <input type="text" name="name" placeholder="Name" disabled={isPending} />
      <br />
      <input type="email" name="email" placeholder="Email" disabled={isPending} />
      <br />
      <button type="submit" disabled={isPending}>
        {isPending ? 'Submitting...' : 'Create User'}
      </button>
      {state.message && <p style={{ color: state.success ? 'green' : 'red' }}>{state.message}</p>}
    </form>
  );
}

// To use this component:
// <UserForm />
```

This example uses `useActionState` to handle form submission. `createUser` is the action function, and `formAction` is passed directly to the form's `action` prop. The `isPending` state disables the form inputs and button during submission, and `state.message` displays the result.

## Experimental and Unstable Hooks

These hooks are currently experimental or unstable and might change in future React releases. They are provided for advanced use cases or for testing upcoming features.

### useInsertionEffect

The `useInsertionEffect` hook is specifically designed for CSS-in-JS libraries. It fires synchronously *before* DOM mutations, allowing libraries to inject new styles into the DOM before any layout calculations or paints occur. This ensures that styles are available before elements are measured or rendered, preventing issues like FOUC (Flash of Unstyled Content).

**Signature**

| Parameter | Type | Description |
|---|---|---|
| `create` | `() => (() => void) \| void` | The effect function, which typically injects styles. It can optionally return a cleanup function. |
| `deps` | `Array<mixed> \| void \| null` | An optional array of dependencies. The effect will re-run if any value in this array changes. |

**Returns**

| Name | Type | Description |
|---|---|---|
| `void` | `void` | This hook does not return a value. |

**Example (Conceptual for CSS-in-JS)**

```javascript
import React, { useInsertionEffect } from 'react';

// Imagine a CSS-in-JS library's internal hook
function useStyleInjection(rule) {
  useInsertionEffect(() => {
    // This is where a CSS-in-JS library would inject a style tag
    const styleTag = document.createElement('style');
    styleTag.innerHTML = rule;
    document.head.appendChild(styleTag);

    return () => {
      document.head.removeChild(styleTag);
    };
  }, [rule]);
}

function StyledComponent() {
  useStyleInjection(`.my-red-text { color: red; }`);
  return <p className="my-red-text">This text is styled by useInsertionEffect (conceptually)</p>;
}

// To use this component:
// <StyledComponent />
```

This conceptual example illustrates how `useInsertionEffect` could be used by a CSS-in-JS library to inject a style rule (`.my-red-text { color: red; }`) into the document's `<head>` *before* the component is painted, ensuring the styling is applied immediately.

### experimental_useEffectEvent

The `experimental_useEffectEvent` hook lets you define an "Effect Event" which is a function that always has the latest props and state without causing an effect to re-run. This is useful for event handlers inside effects that don't need to trigger the effect every time they change, avoiding common lint warnings for missing dependencies.

**Signature**

| Parameter | Type | Description |
|---|---|---|
| `callback` | `F` | The function to be turned into an effect event. This function should contain logic that does not need to be part of the dependency array of its parent effect. |

**Returns**

| Name | Type | Description |
|---|---|---|
| `eventFunction` | `F` | A stable reference to the `callback` function. This returned function will always have access to the latest state and props of the component, but its identity itself is stable across renders. |

**Example**

```javascript
import React, { useEffect, useState, experimental_useEffectEvent } from 'react';

function ChatRoom({ roomId, theme }) {
  const [message, setMessage] = useState('');

  // Define an event handler that needs the latest message and theme
  const onSend = experimental_useEffectEvent(() => {
    // This function will always have the latest 'message' and 'theme'
    // without 'message' or 'theme' being dependencies of the effect below.
    console.log(`Sending '${message}' to room ${roomId} with theme ${theme}`);
    // Actual send logic here
  });

  useEffect(() => {
    // Setup chat connection
    console.log(`Connecting to chat room ${roomId}`);
    const connection = {
      connect: () => console.log('Connected!'),
      disconnect: () => console.log('Disconnected!'),
      onmessage: () => onSend() // Use the stable event function
    };
    connection.connect();

    return () => connection.disconnect();
  }, [roomId]); // roomId is a dependency, but onSend is not needed

  return (
    <div>
      <input
        value={message}
        onChange={e => setMessage(e.target.value)}
        placeholder="Type your message..."
      />
      <button onClick={() => onSend()}>Send</button>
      <p>Current Theme: {theme}</p>
    </div>
  );
}

// To use this component:
// <ChatRoom roomId="general" theme="dark" />
```

In this example, `onSend` is an effect event. It can access the latest `message` and `theme` without being included in `useEffect`'s dependency array. This prevents the effect from re-running unnecessarily every time `message` or `theme` changes, while still ensuring the event handler uses current values.

### unstable_getCacheForType

The `unstable_getCacheForType` function (not strictly a hook, but related to data management) allows you to get a cached resource of a specific type. It's an internal utility for managing React's new caching mechanisms.

**Signature**

| Parameter | Type | Description |
|---|---|---|
| `resourceType` | `() => T` | A function that, when called, produces the resource of type `T`. This function also acts as the key for the cache. |

**Returns**

| Name | Type | Description |
|---|---|---|
| `cachedResource` | `T` | The cached resource of the specified type. If not already cached, `resourceType()` will be called to produce and cache the resource. |

**Example (Conceptual)**

```javascript
import React, { unstable_getCacheForType } from 'react';

// Imagine a resource producer
const createHttpClient = () => {
  console.log('Creating new HTTP client...');
  return { fetch: (url) => `Data from ${url}` };
};

function DataFetcher() {
  // Get or create a cached HTTP client instance
  const httpClient = unstable_getCacheForType(createHttpClient);

  const data = httpClient.fetch('https://api.example.com/data');

  return <p>{data}</p>;
}

// To use this component:
// <DataFetcher />
```

This conceptual example shows how `unstable_getCacheForType` can be used to ensure that `createHttpClient` is only called once and its result is cached and reused across components or renders, optimizing resource creation.

### unstable_useCacheRefresh

The `unstable_useCacheRefresh` hook provides a function that can be used to explicitly refresh the cache for a specific resource type. This is particularly useful for invalidating cached data after mutations or when the underlying data source has changed.

**Signature**

This hook takes no parameters.

**Returns**

| Name | Type | Description |
|---|---|---|
| `refreshCache` | `<T>(?() => T, ?T) => void` | A function to refresh the cache. The first argument is the `resourceType` function (same as in `unstable_getCacheForType`), and the second argument is an optional new value to put into the cache. |

**Example (Conceptual)**

```javascript
import React, { unstable_getCacheForType, unstable_useCacheRefresh } from 'react';

const getUserData = (userId) => {
  console.log(`Fetching user ${userId} data...`);
  return new Promise(resolve => {
    setTimeout(() => resolve({ id: userId, name: `User ${userId}` }), 500);
  });
};

function UserDisplay({ userId }) {
  // This would be wrapped in Suspense and use() to read the promise
  const userData = unstable_getCacheForType(() => getUserData(userId));
  return <p>User: {userData.name}</p>;
}

function UserEditor({ userId }) {
  const refresh = unstable_useCacheRefresh();

  const handleUpdateUser = async () => {
    console.log('Updating user...');
    // Simulate API call to update user
    await new Promise(resolve => setTimeout(resolve, 1000));
    // After update, refresh the cache for this user's data
    refresh(() => getUserData(userId)); // Invalidate the specific cache entry
    alert('User updated, cache refreshed!');
  };

  return (
    <div>
      <UserDisplay userId={userId} />
      <button onClick={handleUpdateUser}>Update User</button>
    </div>
  );
}

// To use this component:
// <UserEditor userId="123" />
```

This conceptual example shows `unstable_useCacheRefresh` being used to invalidate and refresh the cached data for `getUserData(userId)` after an update operation. This ensures that `UserDisplay` will fetch the latest data after the update.

### unstable_useMemoCache

The `unstable_useMemoCache` hook is an internal utility provided for compiler-optimized memoization, specifically for use by the React Compiler Runtime. It provides a fixed-size array that can be used to store memoized values, allowing the compiler to generate more efficient memoization logic.

**Signature**

| Parameter | Type | Description |
|---|---|---|
| `size` | `number` | The desired size of the memo cache array. |

**Returns**

| Name | Type | Description |
|---|---|---|
| `cacheArray` | `Array<mixed>` | A fixed-size array for storing memoized values. |

**Example (Conceptual for Compiler Runtime)**

```javascript
// This hook is primarily for internal use by React Compiler Runtime.
// You typically won't use it directly in your application code.

// import { unstable_useMemoCache as c } from 'react'; // Renamed for compiler use

// function MyComponent(props) {
//   const cache = c(2); // Request a cache array of size 2

//   // The compiler would generate code like this:
//   // if (cache[0] !== props.value) {
//   //   cache[0] = props.value;
//   //   cache[1] = expensiveCalculation(props.value);
//   // }
//   // const result = cache[1];

//   // return <div>{result}</div>;
// }
```

This hook is generally not meant for direct application use. It serves as a building block for React's experimental compiler, allowing it to optimize components by providing a dedicated, efficient memory space for memoization.

---

This section has provided a comprehensive overview of React's client-side hooks, covering both standard and experimental APIs for various state management, side effect, and performance optimization scenarios. Understanding these hooks is crucial for building efficient and maintainable React applications.

Next, explore the built-in components and element utilities in the [Components and Elements](./client-apis-components-elements.md) section.