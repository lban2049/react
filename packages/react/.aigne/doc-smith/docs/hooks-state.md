# State Hooks

State is the memory of a component. It allows your components to be interactive by tracking data that changes over time. State Hooks provide the tools to declare, update, and manage this data within your function components. This section covers the primary hooks for managing component state, from simple values to complex logic and asynchronous actions.

For side effects related to state changes, see [Effect Hooks](./hooks-effect.md).

<x-cards data-columns="2">
  <x-card data-title="useState" data-icon="lucide:variable">
    The most fundamental hook for adding state to a component. Ideal for simple state values like numbers, strings, or booleans.
  </x-card>
  <x-card data-title="useReducer" data-icon="lucide:git-commit-vertical">
    An alternative to `useState` for managing more complex state logic, especially when the next state depends on the previous one.
  </x-card>
  <x-card data-title="useActionState" data-icon="lucide:form-input">
    Designed for handling form submissions and other actions, managing pending states, and handling the returned result of an action.
  </x-card>
  <x-card data-title="useOptimistic" data-icon="lucide:fast-forward">
    Lets you provide a better user experience by immediately showing the outcome of an action before the asynchronous operation completes.
  </x-card>
</x-cards>


## useState

`useState` is the most common hook for managing state. You call it at the top level of your component to declare a state variable.

```javascript
function useState<S>(
  initialState: (() => S) | S,
): [S, Dispatch<BasicStateAction<S>>]
```

**Parameters**

| Name | Type | Description |
|---|---|---|
| `initialState` | `S` or `() => S` | The initial value of the state. It can be a value of any type, or a function that returns the initial value. The function is only executed during the initial render. |

**Returns**

An array with exactly two items:
1.  The current state value.
2.  A `dispatch` function that lets you update the state to a new value and trigger a re-render.

**Example: A Simple Counter**

```javascript
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

In this example, `useState(0)` initializes the `count` state variable to `0`. Clicking the button calls `setCount` with the new value, which updates the state and re-renders the component to display the new count.

## useReducer

`useReducer` is typically preferred over `useState` when you have complex state logic that involves multiple sub-values or when the next state depends on the previous one.

```javascript
function useReducer<S, I, A>(
  reducer: (S, A) => S,
  initialArg: I,
  init?: I => S,
): [S, Dispatch<A>]
```

**Parameters**

| Name | Type | Description |
|---|---|---|
| `reducer` | `(S, A) => S` | A function that specifies how the state gets updated. It must be pure, take the state and action as arguments, and return the next state. |
| `initialArg` | `I` | The value from which the initial state is calculated. |
| `init` | `I => S` | (Optional) An initializer function that returns the initial state. If not provided, `initialArg` is used as the initial state. |

**Returns**

An array with exactly two items:
1.  The current state value.
2.  A `dispatch` function to which you can pass an `action` and trigger a state update.

**Example: Managing Complex State**

```javascript
import { useReducer } from 'react';

const initialState = {count: 0};

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return {count: state.count + 1};
    case 'decrement':
      return {count: state.count - 1};
    default:
      throw new Error();
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <>
      Count: {state.count}
      <button onClick={() => dispatch({type: 'decrement'})}>-</button>
      <button onClick={() => dispatch({type: 'increment'})}>+</button>
    </>
  );
}
```
This example centralizes all state update logic into the `reducer` function, making the component's event handlers cleaner as they only need to `dispatch` actions.

## useActionState

This hook is designed to manage the state of actions, such as form submissions. It provides information about the pending state of the action and the data returned after it completes.

```javascript
function useActionState<S, P>(
  action: (Awaited<S>, P) => S,
  initialState: Awaited<S>,
  permalink?: string,
): [Awaited<S>, (P) => void, boolean]
```

**Parameters**

| Name | Type | Description |
|---|---|---|
| `action` | `(S, P) => S` | The function to be executed. It receives the previous state and the action's payload. |
| `initialState` | `S` | The initial state value. |
| `permalink` | `string` | (Optional) A URL to be used in progressive enhancement scenarios. |

**Returns**

An array with three items:
1.  The current state of the action. It is `initialState` before the action is called, and the return value of the action after it completes.
2.  A `dispatch` function to call the action.
3.  A boolean `isPending` value that is `true` while the action is executing.

**Example: Form Submission**
```javascript
import { useActionState } from 'react';

async function updateUser(previousState, formData) {
  // Simulate a network request
  await new Promise(res => setTimeout(res, 500)); 
  const name = formData.get('name');
  if (name.length < 3) {
    return { success: false, message: 'Name must be longer.' };
  }
  return { success: true, message: `Welcome, ${name}!` };
}

function ChangeNameForm() {
  const [state, submitAction, isPending] = useActionState(
    updateUser,
    { success: null, message: '' }
  );

  return (
    <form action={submitAction}>
      <input type="text" name="name" />
      <button type="submit" disabled={isPending}>
        {isPending ? 'Saving...' : 'Update'}
      </button>
      {state.message && <p>{state.message}</p>}
    </form>
  );
}
```

## useOptimistic

`useOptimistic` allows you to apply a temporary, "optimistic" state update that is immediately rendered to the user while an asynchronous action is in progress. If the action fails, the state automatically reverts.

```javascript
function useOptimistic<S, A>(
  passthrough: S,
  reducer: ?(S, A) => S,
): [S, (A) => void]
```

**Parameters**

| Name | Type | Description |
|---|---|---|
| `passthrough` | `S` | The default state value to be used when no optimistic update is active. |
| `reducer` | `(S, A) => S` | A function that takes the current state and the optimistic value and returns the new optimistic state. |

**Returns**

An array with two items:
1.  The optimistic state, which will be the same as `passthrough` unless an action is pending.
2.  An `addOptimistic` function to apply a temporary state update.

**Example: Optimistically Adding a Message**
```javascript
import { useOptimistic, useState, useRef } from 'react';

async function deliverMessage(message) {
  // Simulate network delay
  await new Promise(res => setTimeout(res, 1000));
  // To simulate an error, you could throw an error here.
  return message;
}

function MessageThread() {
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
    const sentMessage = await deliverMessage(message);
    setMessages(prev => [...prev, { text: sentMessage }]);
  }

  return (
    <div>
      {optimisticMessages.map((m, i) => (
        <div key={i}>{m.text}{m.sending && <small> (Sending...)</small>}</div>
      ))}
      <form action={formAction} ref={formRef}>
        <input type="text" name="message" placeholder="Hello!" />
        <button type="submit">Send</button>
      </form>
    </div>
  );
}
```
When the user sends a message, it appears in the list immediately with a "Sending..." label. Once the async `deliverMessage` function completes, the optimistic state is replaced by the actual state from `setMessages`.

---

Now that you understand how to manage state, the next step is to learn how to handle side effects that occur as a result of state changes or component lifecycle events. Continue to the [Effect Hooks](./hooks-effect.md) section to learn more.