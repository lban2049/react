# State Hooks

State is the heart of any interactive React application. State Hooks provide a way to retain and manage data within your function components across re-renders. They allow your components to respond to user input, network responses, and any other changes over time.

This guide covers the primary hooks for managing component state. For a foundational understanding of how state and props work, you may want to review [Components & Props](./core-apis-components-and-props.md).

<x-cards data-columns="2">
  <x-card data-title="useState" data-icon="lucide:variable">
    The most common Hook for managing simple state values like numbers, strings, or booleans.
  </x-card>
  <x-card data-title="useReducer" data-icon="lucide:network">
    Ideal for managing complex state logic with multiple sub-values or when the next state depends on the previous one.
  </x-card>
  <x-card data-title="useOptimistic" data-icon="lucide:fast-forward">
    Enhances user experience by immediately reflecting a state change, then reverting if the underlying asynchronous action fails.
  </x-card>
  <x-card data-title="useActionState" data-icon="lucide:edit">
    Manages the state of form actions, tracking pending states and responses from the server.
  </x-card>
</x-cards>


## useState

`useState` is the most fundamental State Hook. It declares a “state variable,” and you can update it directly to trigger a re-render.

### Signature

```javascript
const [state, setState] = useState(initialState);
```

**Parameters**

| Name | Type | Description |
|---|---|---|
| `initialState` | `S` or `() => S` | The initial value of the state. It can be a value of any type. It can also be a function, which will be executed only during the initial render to compute the initial state. |

**Returns**

An array containing two elements:
1.  **The current state:** The value of your state variable for the current render.
2.  **The `setState` function:** A function that lets you update the state to a new value and trigger a re-render.

### Example: A Simple Counter

```javascript
import { useState } from 'react';

function Counter() {
  // Declare a new state variable, which we'll call "count"
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
In this example, `useState(0)` initializes the `count` state variable to `0`. When the user clicks the button, `setCount(count + 1)` is called, which updates the state and causes the component to re-render with the new count value.

## useReducer

`useReducer` is an alternative to `useState` for managing more complex state logic. It is particularly useful when you have multiple sub-values or when the next state depends on the previous one. It follows the Redux pattern of using a reducer function to manage state transitions.

### Signature

```javascript
const [state, dispatch] = useReducer(reducer, initialArg, init?);
```

**Parameters**

| Name | Type | Description |
|---|---|---|
| `reducer` | `(S, A) => S` | A function that specifies how the state gets updated. It receives the current state and an action, and should return the next state. |
| `initialArg` | `I` | The value from which the initial state is calculated. |
| `init` | `(I) => S` | (Optional) An initializer function that returns the initial state. If not provided, the initial state is set to `initialArg`. |

**Returns**

An array containing two elements:
1.  **The current state:** The current state value.
2.  **The `dispatch` function:** A function that you can call with an action to update the state.

### Example: Managing Complex State

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
Here, all the state update logic is centralized in the `reducer` function. The component calls `dispatch` with an action object to trigger state updates, making the component's event handlers cleaner and the state logic more predictable.

## useOptimistic

`useOptimistic` is a Hook that lets you show a different state to the user while an asynchronous action is underway. It takes the current state and provides an "optimistic" version of it that can be updated immediately. This optimistic state is used until the async operation (e.g., a network request) completes, at which point the state updates to the final value.

### Signature

```javascript
const [optimisticState, addOptimistic] = useOptimistic(passthrough, reducer?);
```

**Parameters**

| Name | Type | Description |
|---|---|---|
| `passthrough` | `S` | The value that will be returned when no optimistic update is active. This is typically your actual state. |
| `reducer` | `(S, A) => S` | (Optional) A function that takes the current state and the value passed to `addOptimistic`, and returns the new optimistic state. |

**Returns**

An array containing two elements:
1.  **`optimisticState`:** The optimistic state value. It will equal `passthrough` unless an update is active.
2.  **`addOptimistic`:** A function to call with an update value to immediately change the `optimisticState`.

### Example: Optimistically Adding a Message

```javascript
import { useOptimistic, useState, useRef } from 'react';

async function deliverMessage(message) {
  // Simulate a network request
  await new Promise(res => setTimeout(res, 1000));
  // For this demo, we'll say it always fails to show the revert behavior
  throw new Error('Message could not be sent'); 
}

function Thread({ messages, setMessages }) {
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
      // This is where you would send the message to the server
      await deliverMessage(message);
      // On success, update the real state
      setMessages(prev => [...prev, { text: message }]);
    } catch (e) {
      // The optimistic update is automatically reverted on error
      console.error(e);
    }
  }

  return (
    <>
      {optimisticMessages.map((m, i) => (
        <div key={i}>{m.text}{m.sending && <small> (Sending...)</small>}</div>
      ))}
      <form action={formAction} ref={formRef}>
        <input type="text" name="message" />
        <button type="submit">Send</button>
      </form>
    </>
  );
}
```
When the user submits the form, the message appears in the list instantly with a "(Sending...)" label. Because our `deliverMessage` function fails, React automatically reverts the UI to the last known real state (`messages`).

## useActionState

`useActionState` is a Hook for managing the state of form actions. It provides the state from the last form submission, the action to be passed to the `<form>`, and the pending status of the form.

### Signature

```javascript
const [state, formAction, isPending] = useActionState(action, initialState, permalink?);
```

**Parameters**

| Name | Type | Description |
|---|---|---|
| `action` | `(S, P) => S` | The function to be executed when the form is submitted. It receives the previous state and the form's payload. |
| `initialState` | `S` | The initial state value. |
| `permalink` | `string` | (Optional) A URL to redirect to upon a successful form submission. |

**Returns**

An array containing three elements:
1.  **`state`:** The current state. It matches `initialState` on first render, and the return value of the last action after that.
2.  **`formAction`:** The action to pass to your `<form>`'s `action` prop.
3.  **`isPending`:** A boolean indicating whether the form action is currently pending.

### Example: Handling Form Submission State

```javascript
import { useActionState } from 'react';

async function increment(previousState, formData) {
  // Simulate async work
  await new Promise(res => setTimeout(res, 500));
  return previousState + 1;
}

function StatefulForm() {
  const [count, formAction, isPending] = useActionState(increment, 0);

  return (
    <form action={formAction}>
      <p>Count: {count}</p>
      <button type="submit" disabled={isPending}>
        {isPending ? 'Incrementing...' : 'Increment'}
      </button>
    </form>
  );
}
```
This example shows a form where a button click increments a counter. While the async `increment` action is running, `isPending` is `true`, allowing the UI to disable the button and show a loading message.

## Choosing Your State Hook

Deciding which state hook to use depends on the complexity of your component's state.

```d2
direction: down

State-Complexity: {
  label: "How complex is your component's state?"
  shape: diamond
}

Simple-State: {
  label: "Simple State\n(boolean, string, number)"
  shape: rectangle
}

Complex-State: {
  label: "Complex State\n(object, array, multiple interdependent values)"
  shape: rectangle
}

Use-useState: {
  label: "Use useState"
  shape: oval
  tooltip: "Best for simple, independent state values."
}

Use-useReducer: {
  label: "Use useReducer"
  shape: oval
  tooltip: "Best for predictable state transitions and complex logic."
}

Needs-Optimistic-UI: {
  label: "Does it need an optimistic UI for async actions?"
  shape: diamond
}

Use-useOptimistic: {
  label: "Use useOptimistic"
  shape: oval
  tooltip: "Improves perceived performance for actions with latency."
}

Is-Form-Action: {
  label: "Is the state tied to a form action?"
  shape: diamond
}

Use-useActionState: {
  label: "Use useActionState"
  shape: oval
  tooltip: "Manages form submission state, including pending status."
}

State-Complexity -> Simple-State: "Simple"
State-Complexity -> Complex-State: "Complex"

Simple-State -> Use-useState
Complex-State -> Use-useReducer

Use-useState -> Needs-Optimistic-UI
Use-useReducer -> Needs-Optimistic-UI

Needs-Optimistic-UI -> Use-useOptimistic: "Yes"
Needs-Optimistic-UI -> Is-Form-Action: "No"

Is-Form-Action -> Use-useActionState: "Yes"
```

*   **Start with `useState`:** It's perfect for simple, independent state values.
*   **Upgrade to `useReducer`:** When state logic becomes complex, involves multiple sub-values, or when the next state depends on the previous one in a non-trivial way.
*   **Add `useOptimistic`:** When you need to provide immediate feedback for an asynchronous action to improve the user experience.
*   **Use `useActionState`:** When managing state specifically related to form submissions, especially when you need to track pending states and handle server responses.

---

Now that you understand how to manage state, the next step is learning how to handle side effects, such as fetching data or subscribing to events. Continue to the [Effect Hooks](./hooks-effect.md) guide to learn more.