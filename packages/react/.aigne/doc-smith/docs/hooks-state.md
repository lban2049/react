# State Hooks

State Hooks provide a way to manage local state within your function components. They allow you to add stateful logic to components without converting them into classes. This section covers the primary hooks for managing state, from simple values to complex state transitions and form interactions.

For a general overview of all available Hooks, you can refer to the main [Hooks](./hooks.md) documentation.

<x-cards data-columns="2">
  <x-card data-title="useState" data-icon="lucide:variable">
    The most fundamental hook for managing simple local state.
  </x-card>
  <x-card data-title="useReducer" data-icon="lucide:binary">
    An alternative to useState for managing more complex state logic.
  </x-card>
  <x-card data-title="useOptimistic" data-icon="lucide:fast-forward">
    For updating the UI instantly without waiting for an asynchronous action to complete.
  </x-card>
  <x-card data-title="useActionState" data-icon="lucide:form-input">
    Designed to manage the state of forms and the actions they trigger.
  </x-card>
</x-cards>


## useState

The `useState` hook is the most common way to add state to a function component. You call it to declare a single piece of state, and it returns a pair of values: the current state and a function to update it.

### API Reference

```javascript
const [state, setState] = useState(initialState);
```

**Parameters**

| Parameter      | Type                | Description                                                                                                                              |
|----------------|---------------------|------------------------------------------------------------------------------------------------------------------------------------------|
| `initialState` | `S` or `() => S`    | The initial value for the state. If you pass a function, it will be executed only during the initial render to compute the initial state. |

**Returns**

An array containing two elements:

| Index | Name       | Type                                | Description                                                                                                                           |
|-------|------------|-------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------|
| 0     | `state`    | `S`                                 | The current value of the state for the current render.                                                                                |
| 1     | `setState` | `Dispatch<BasicStateAction<S>>`     | A function that lets you update the state. You can pass a new value directly, or a function that receives the previous state and returns the new state. |

### Example

Here is a simple counter component that uses `useState` to keep track of the count.

```javascript Counter Component icon=logos:javascript
import { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

## useReducer

`useReducer` is an alternative to `useState` that is better suited for managing complex state logic involving multiple sub-values or when the next state depends on the previous one. It follows a pattern similar to Redux.

### API Reference

```javascript
const [state, dispatch] = useReducer(reducer, initialArg, init?);
```

**Parameters**

| Parameter    | Type             | Description                                                                                                     |
|--------------|------------------|-----------------------------------------------------------------------------------------------------------------|
| `reducer`    | `(S, A) => S`    | A function that accepts the current state (`S`) and an action (`A`), and returns the new state.                 |
| `initialArg` | `I`              | The initial argument passed to the `init` function, or the initial state if `init` is not provided.           |
| `init`       | `(I) => S`       | (Optional) An initializer function that returns the initial state. It allows for extracting complex state initialization logic. |

**Returns**

An array containing two elements:

| Index | Name       | Type          | Description                                                                                                   |
|-------|------------|---------------|---------------------------------------------------------------------------------------------------------------|
| 0     | `state`    | `S`           | The current value of the state.                                                                               |
| 1     | `dispatch` | `Dispatch<A>` | A function that you can call with an action to trigger a state update by invoking the reducer function.      |

### Example

This counter example is refactored to use `useReducer` to handle both incrementing and decrementing the count.

```javascript Reducer Counter icon=logos:javascript
import { useReducer } from 'react';

const initialState = { count: 0 };

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 };
    case 'decrement':
      return { count: state.count - 1 };
    default:
      throw new Error();
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <div>
      <p>Count: {state.count}</p>
      <button onClick={() => dispatch({ type: 'increment' })}>+</button>
      <button onClick={() => dispatch({ type: 'decrement' })}>-</button>
    </div>
  );
}
```

## useOptimistic

`useOptimistic` is a hook for managing optimistic UI updates. It allows your interface to respond immediately to a user's action, while the actual data mutation happens asynchronously in the background. If the background operation fails, the UI automatically reverts to its previous state.

### API Reference

```javascript
const [optimisticState, addOptimistic] = useOptimistic(passthrough, reducer?);
```

**Parameters**

| Parameter     | Type                | Description                                                                                                                                 |
|---------------|---------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| `passthrough` | `S`                 | The default state that is returned when no optimistic update is in progress. This is typically state managed by `useState` or passed via props. |
| `reducer`     | `?(S, A) => S`      | (Optional) A function that takes the current state and an action, and returns the new optimistic state.                                   |

**Returns**

An array containing two elements:

| Index | Name              | Type           | Description                                                                                                                   |
|-------|-------------------|----------------|-------------------------------------------------------------------------------------------------------------------------------|
| 0     | `optimisticState` | `S`            | The state value. It will reflect the optimistic value during the async action, otherwise it will be the `passthrough` state.     |
| 1     | `addOptimistic`   | `(A) => void`  | A function to call with an action to trigger an optimistic update.                                                            |

### Example

Imagine a chat application where new messages appear instantly, even before they are confirmed by the server.

```javascript Optimistic Chat icon=logos:javascript
import { useOptimistic, useState, useRef } from 'react';

async function sendMessage(message) {
  // Simulate a network request
  await new Promise(res => setTimeout(res, 1000));
  // For demo, let's pretend it can fail
  if (message.includes('error')) {
    throw new Error('Failed to send message');
  }
  return { text: message, sending: false };
}

function Chat() {
  const [messages, setMessages] = useState([]);
  const [optimisticMessages, addOptimisticMessage] = useOptimistic(
    messages,
    (state, newMessage) => [
      ...state,
      { text: newMessage, sending: true }
    ]
  );
  const formRef = useRef();

  async function formAction(formData) {
    const message = formData.get('message');
    addOptimisticMessage(message);
    formRef.current.reset();
    try {
      const sentMessage = await sendMessage(message);
      setMessages(prev => [...prev, sentMessage]);
    } catch (e) {
      // The UI will automatically revert on error
      console.error(e);
    }
  }

  return (
    <div>
      {optimisticMessages.map((m, i) => (
        <div key={i}>{m.text} {m.sending && <small>(Sending...)</small>}</div>
      ))}
      <form action={formAction} ref={formRef}>
        <input type="text" name="message" />
        <button type="submit">Send</button>
      </form>
    </div>
  );
}
```

## useActionState

`useActionState` is a hook for managing the state of form actions. It provides the pending state of the action and the data returned after it completes.

### API Reference

```javascript
const [state, dispatch, isPending] = useActionState(action, initialState, permalink?);
```

**Parameters**

| Parameter      | Type                | Description                                                                                                   |
|----------------|---------------------|---------------------------------------------------------------------------------------------------------------|
| `action`       | `(S, P) => S`       | The function to be executed. It receives the previous state and the action's payload (e.g., form data).     |
| `initialState` | `S`                 | The initial state value.                                                                                      |
| `permalink`    | `?string`           | (Optional) A URL to redirect to for server-side form submissions.                                             |

**Returns**

An array containing three elements:

| Index | Name        | Type           | Description                                                                             |
|-------|-------------|----------------|-----------------------------------------------------------------------------------------|
| 0     | `state`     | `S`            | The current state of the action. It holds the value returned by the last action execution. |
| 1     | `dispatch`  | `(P) => void`  | The function to pass to the `<form>` `action` prop to trigger the action.               |
| 2     | `isPending` | `boolean`      | A boolean that is `true` while the action is pending, and `false` otherwise.             |

### Example

This example shows a form for updating a username. The `useActionState` hook manages the pending state and any error messages returned from the server action.

```javascript Action State Form icon=logos:javascript
import { useActionState } from 'react';

async function updateName(previousState, formData) {
  const newName = formData.get('name');
  if (newName.length < 3) {
    return { error: 'Name must be at least 3 characters long.' };
  }
  // Simulate async update
  await new Promise(res => setTimeout(res, 500));
  return { error: null, success: `Name changed to ${newName}` };
}

function ChangeNameForm() {
  const [state, formAction, isPending] = useActionState(updateName, { error: null });

  return (
    <form action={formAction}>
      <label htmlFor="name">Name:</label>
      <input id="name" name="name" />
      <button type="submit" disabled={isPending}>
        {isPending ? 'Updating...' : 'Update'}
      </button>
      {state?.error && <p style={{ color: 'red' }}>{state.error}</p>}
      {state?.success && <p style={{ color: 'green' }}>{state.success}</p>}
    </form>
  );
}

```

---

These State Hooks form the basis for creating dynamic and interactive components in React. After mastering state, the next step is to manage side effects, such as fetching data or setting up subscriptions. Continue to the [Effect Hooks](./hooks-effect.md) section to learn more.