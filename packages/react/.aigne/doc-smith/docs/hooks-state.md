# State Hooks

State Hooks allow you to add stateful logic to your function components, enabling them to remember information and re-render in response to changes. This section covers the primary Hooks for managing component state, from simple values to complex state transitions and form interactions.

For an introduction to the concept of state in React, you might want to review the core concepts in the [Components & Props](./core-apis-components-and-props.md) guide.

<x-cards data-columns="2">
  <x-card data-title="useState" data-icon="lucide:variable">
    For basic state management, such as numbers, strings, or booleans.
  </x-card>
  <x-card data-title="useReducer" data-icon="lucide:workflow">
    Ideal for managing complex state logic with multiple sub-values or when the next state depends on the previous one.
  </x-card>
  <x-card data-title="useOptimistic" data-icon="lucide:fast-forward">
    Used for creating responsive user interfaces by updating the UI immediately, before the underlying asynchronous operation completes.
  </x-card>
  <x-card data-title="useActionState" data-icon="lucide:form-input">
    Designed for handling form submissions, providing access to the form's pending state and the result of the action.
  </x-card>
</x-cards>

## useState

`useState` is the most common Hook for adding state to a function component. It declares a “state variable” and returns a pair of values: the current state and a function that lets you update it.

### Signature
`useState<S>(initialState: (() => S) | S): [S, Dispatch<BasicStateAction<S>>]`

### Parameters

| Parameter      | Type                | Description                                                                                             |
| -------------- | ------------------- | ------------------------------------------------------------------------------------------------------- |
| `initialState` | `S` or `(() => S)` | The initial value of the state. It can be a value of any type, or a function that returns the initial value. The function is only executed during the initial render. |

### Returns
An array containing two elements:

| Index | Name       | Type                          | Description                                                                                                                                              |
| ----- | ---------- | ----------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 0     | `state`    | `S`                           | The current value of the state. During the first render, it will be equal to `initialState`.                                                             |
| 1     | `setState` | `Dispatch<BasicStateAction<S>>` | The setter function. You can call it to update the state, which will trigger a re-render of the component. It accepts a new value or a function that receives the previous state. |

### Example
Here is a simple counter component that uses `useState` to track the number of clicks.

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
In this example, `useState(0)` initializes the `count` state to `0`. Each time the button is clicked, `setCount(count + 1)` is called, updating the state and causing the component to re-render with the new count.

## useReducer

`useReducer` is an alternative to `useState` for managing more complex state logic. It is particularly useful when you have complex state logic that involves multiple sub-values or when the next state depends on the previous one.

### Signature
`useReducer<S, I, A>(reducer: (S, A) => S, initialArg: I, init?: I => S): [S, Dispatch<A>]`

### Parameters

| Parameter    | Type                        | Description                                                                                                    |
| ------------ | --------------------------- | -------------------------------------------------------------------------------------------------------------- |
| `reducer`    | `(S, A) => S`               | A pure function that accepts the current state and an action, and returns the new state.                       |
| `initialArg` | `I`                         | The value from which the initial state is calculated.                                                          |
| `init`       | `(I) => S`                  | *Optional.* An initializer function that returns the initial state. If not provided, `initialArg` is used as the initial state. |

### Returns
An array containing two elements:

| Index | Name       | Type         | Description                                                          |
| ----- | ---------- | ------------ | -------------------------------------------------------------------- |
| 0     | `state`    | `S`          | The current state value.                                             |
| 1     | `dispatch` | `Dispatch<A>` | A function that you can call with an `action` to update the state. |

### Example
A counter that can be incremented or decremented using a reducer.

```javascript
import { useReducer } from 'react';

const initialState = { count: 0 };

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 };
    case 'decrement':
      return { count: state.count - 1 };
    default:
      throw new Error('Unsupported action type');
  }
}

function ReducerCounter() {
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <div>
      <p>Count: {state.count}</p>
      <button onClick={() => dispatch({ type: 'decrement' })}>-</button>
      <button onClick={() => dispatch({ type: 'increment' })}>+</button>
    </div>
  );
}
```
Here, all state update logic is centralized within the `reducer` function. The component calls `dispatch` with an action object to signal its intent to change the state.

## useOptimistic

`useOptimistic` is a Hook that lets you show a different state while an asynchronous action is underway. It takes the current state and returns a copy of it that can be different during the action's duration.

### Signature
`useOptimistic<S, A>(passthrough: S, reducer: ?(S, A) => S): [S, (A) => void]`

### Parameters

| Parameter     | Type              | Description                                                                                                    |
| ------------- | ----------------- | -------------------------------------------------------------------------------------------------------------- |
| `passthrough` | `S`               | The state value that will be returned by default when no action is pending.                                  |
| `reducer`     | `?(S, A) => S`    | *Optional.* A function that takes the current state and the action payload, and returns the new optimistic state. If not provided, the action payload itself becomes the optimistic state. |

### Returns
An array containing two elements:

| Index | Name              | Type             | Description                                                                                                                  |
| ----- | ----------------- | ---------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| 0     | `optimisticState` | `S`              | The optimistic state. It will be equal to `passthrough` unless an action is pending, in which case it is calculated by the `reducer`. |
| 1     | `addOptimistic`   | `(A) => void`    | A dispatch function to call when you want to trigger an optimistic update. It takes an action payload of type `A`.        |

### Example
An example of a message list that optimistically adds a new message while it's being sent to the server.

```javascript
import { useOptimistic, useState, useRef } from 'react';

async function sendMessage(message) {
  // Simulate a network delay
  await new Promise(res => setTimeout(res, 1000));
  return message;
}

function MessageList() {
  const formRef = useRef(null);
  const [messages, setMessages] = useState([]);
  const [optimisticMessages, addOptimisticMessage] = useOptimistic(
    messages,
    (currentMessages, newMessage) => [
      ...currentMessages,
      { text: newMessage, sending: true },
    ]
  );

  const formAction = async (formData) => {
    const message = formData.get('message');
    addOptimisticMessage(message);
    formRef.current.reset();
    await sendMessage(message);
    setMessages(prev => [...prev, { text: message }]);
  };

  return (
    <div>
      {optimisticMessages.map((msg, index) => (
        <div key={index}>
          {msg.text}
          {msg.sending && <small> (Sending...)</small>}
        </div>
      ))}
      <form action={formAction} ref={formRef}>
        <input type="text" name="message" placeholder="Your message" />
        <button type="submit">Send</button>
      </form>
    </div>
  );
}
```
When the user submits the form, `addOptimisticMessage` is called, immediately adding a new message with a "Sending..." label to the UI. Once the `sendMessage` function completes, the actual state is updated with `setMessages`, and the optimistic UI merges with the final state.

## useActionState

`useActionState` is a Hook that allows you to update state based on the result of a form action. It is useful for handling form submissions and managing the pending state and response of server actions.

### Signature
`useActionState<S, P>(action: (S, P) => S, initialState: S, permalink?: string): [S, (P) => void, boolean]`

### Returns
An array containing three elements:

| Index | Name        | Type              | Description                                                                                               |
| ----- | ----------- | ----------------- | --------------------------------------------------------------------------------------------------------- |
| 0     | `state`     | `S`               | The current state. On initial render, it's the `initialState`. After the action runs, it's the action's return value. |
| 1     | `formAction` | `(P) => void`     | A new action that you pass to your `<form>` component's `action` prop.                                    |
| 2     | `isPending` | `boolean`         | A boolean indicating whether the form action is currently pending (i.e., the action function is executing). |

### Example
A form that uses `useActionState` to manage its submission state and display a response.

```javascript
import { useActionState } from 'react';

async function addToCart(previousState, formData) {
  const itemId = formData.get('itemId');
  // Simulate an API call
  await new Promise(resolve => setTimeout(resolve, 500));

  if (itemId === '123') {
    return { success: true, message: 'Item added to cart!' };
  }
  return { success: false, message: 'Failed to add item. Please try again.' };
}

function AddToCartForm() {
  const [state, formAction, isPending] = useActionState(addToCart, { success: null, message: '' });

  return (
    <form action={formAction}>
      <input type="hidden" name="itemId" value="123" />
      <button type="submit" disabled={isPending}>
        {isPending ? 'Adding...' : 'Add to Cart'}
      </button>
      {state.message && <p>{state.message}</p>}
    </form>
  );
}
```
This example shows a form that submits an item to a cart. The `isPending` state is used to disable the button during submission, and the `state` object holds the response from the `addToCart` action, which is then displayed to the user.

---

Now that you have learned how to manage component state, the next step is to understand how to handle side effects like data fetching or subscriptions. Proceed to the [Effect Hooks](./hooks-effect.md) documentation to learn more.