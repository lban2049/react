# Hooks

Hooks are functions that let you “hook into” React state and lifecycle features from function components. They allow you to use state, effects, context, and other React features without writing a class. Hooks are a fundamental part of building modern React applications, enabling more reusable and composable logic.

Before using Hooks, you should be familiar with two main rules:
1.  Only call Hooks at the top level of your function components or custom Hooks.
2.  Only call Hooks from React function components, not regular JavaScript functions.

For more details, see the official documentation on the [Rules of Hooks](https://react.dev/link/invalid-hook-call).

This section provides a detailed reference for all built-in Hooks available in React.

## State Hooks

State Hooks allow your components to manage local state.

### useState

Returns a stateful value and a function to update it.

**Signature**
```javascript
const [state, setState] = useState(initialState);
```

**Parameters**

<x-field data-name="initialState" data-type="S | () => S" data-required="true" data-desc="The value the state is initialized with. Can be a value of any type, or a function that returns the initial state. The function is only executed during the initial render."></x-field>

**Returns**

An array containing two elements:

<x-field data-name="state" data-type="S" data-required="true" data-desc="The current state. During the first render, it will match the initialState."></x-field>

<x-field data-name="setState" data-type="Dispatch<BasicStateAction<S>>" data-required="true" data-desc="The state setter function. It lets you update the state to a new value and triggers a re-render. It can be called with the new state or a function that receives the previous state and returns the new state."></x-field>

**Example**

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

### useReducer

An alternative to `useState`. Accepts a reducer of type `(state, action) => newState`, and returns the current state paired with a `dispatch` method.

**Signature**
```javascript
const [state, dispatch] = useReducer(reducer, initialArg, init?);
```

**Parameters**

<x-field data-name="reducer" data-type="(S, A) => S" data-required="true" data-desc="A function that specifies how the state gets updated. It must be pure, should take the state and action as arguments, and should return the next state."></x-field>

<x-field data-name="initialArg" data-type="I" data-required="true" data-desc="The value from which the initial state is calculated."></x-field>

<x-field data-name="init" data-type="I => S" data-required="false" data-desc="An optional initializer function. If it’s provided, the initial state will be set to init(initialArg)."></x-field>

**Returns**

An array containing two elements:

<x-field data-name="state" data-type="S" data-required="true" data-desc="The current state."></x-field>

<x-field data-name="dispatch" data-type="Dispatch<A>" data-required="true" data-desc="The dispatch function that you can use to dispatch actions to the reducer."></x-field>

**Example**

```javascript Counter with Reducer icon=logos:javascript
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

### useActionState

Adds state management to a server action, providing pending and response states.

**Signature**
```javascript
const [state, formAction, isPending] = useActionState(action, initialState, permalink?);
```

**Parameters**

<x-field data-name="action" data-type="(Awaited<S>, P) => S" data-required="true" data-desc="The function to be executed when the form is submitted or a button is clicked."></x-field>

<x-field data-name="initialState" data-type="Awaited<S>" data-required="true" data-desc="The initial state value."></x-field>

<x-field data-name="permalink" data-type="string" data-required="false" data-desc="An optional URL to redirect to after the action completes."></x-field>

**Returns**

An array containing three elements:

<x-field data-name="state" data-type="Awaited<S>" data-required="true" data-desc="The current state. Initially the initialState, and after the action is called, the return value of the action."></x-field>

<x-field data-name="formAction" data-type="(P) => void" data-required="true" data-desc="The action to pass to your <form> component."></x-field>

<x-field data-name="isPending" data-type="boolean" data-required="true" data-desc="A boolean indicating if the action is currently pending. It is true during the action's execution."></x-field>

### useOptimistic

Allows you to optimistically update the UI before a background operation (like a network request) completes.

**Signature**
```javascript
const [optimisticState, addOptimistic] = useOptimistic(passthrough, reducer?);
```

**Parameters**

<x-field data-name="passthrough" data-type="S" data-required="true" data-desc="The state that will be returned when no optimistic update is in progress."></x-field>

<x-field data-name="reducer" data-type="(S, A) => S" data-required="false" data-desc="A reducer function that takes the current state and the optimistic value, and returns the new optimistic state."></x-field>

**Returns**

An array containing two elements:

<x-field data-name="optimisticState" data-type="S" data-required="true" data-desc="The optimistic state value. It will be equal to the `passthrough` state unless an update is active."></x-field>

<x-field data-name="addOptimistic" data-type="(A) => void" data-required="true" data-desc="A function to call with the value you want to optimistically apply."></x-field>

## Effect Hooks

Effect Hooks let you perform side effects in function components, such as data fetching, subscriptions, or manually changing the DOM.

### useEffect

Accepts a function that contains imperative, possibly effectful code. The function runs after render is committed to the screen.

**Signature**
```javascript
useEffect(create, deps?);
```

**Parameters**

<x-field data-name="create" data-type="() => (() => void) | void" data-required="true" data-desc="A function to run as a side effect. It can optionally return a cleanup function, which React will run when the component unmounts or before re-running the effect due to dependency changes."></x-field>

<x-field data-name="deps" data-type="Array<mixed>" data-required="false" data-desc="An optional array of dependencies. The effect will only re-run if one of the dependencies has changed since the last render. If omitted, the effect runs after every render."></x-field>

**Example**

```javascript Document Title Updater icon=logos:javascript
import { useState, useEffect } from 'react';

function TitleUpdater() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `You clicked ${count} times`;
  }, [count]); // Only re-run the effect if count changes

  return (
    <div>
      <p>Check the document title!</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

### useLayoutEffect

The signature is identical to `useEffect`, but it fires synchronously after all DOM mutations. Use this to read layout from the DOM and synchronously re-render.

**Signature**
```javascript
useLayoutEffect(create, deps?);
```

### useInsertionEffect

The signature is identical to `useEffect`, but it fires synchronously before any DOM mutations. This is for CSS-in-JS library authors to inject styles. This should be used sparingly.

**Signature**
```javascript
useInsertionEffect(create, deps?);
```

## Ref Hooks

Ref Hooks provide a way to access DOM nodes or persist mutable values across renders.

### useRef

Returns a mutable ref object whose `.current` property is initialized to the passed argument (`initialValue`). The returned object will persist for the full lifetime of the component.

**Signature**
```javascript
const refContainer = useRef(initialValue);
```

**Parameters**

<x-field data-name="initialValue" data-type="T" data-required="true" data-desc="The initial value for the ref's `current` property."></x-field>

**Returns**

<x-field data-name="refObject" data-type="{ current: T }" data-required="true" data-desc="A ref object with a single mutable `current` property."></x-field>

**Example**

```javascript Focus Input icon=logos:javascript
import { useRef, useEffect } from 'react';

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
```

### useImperativeHandle

Customizes the instance value that is exposed to parent components when using `ref`. As always, imperative code using refs should be avoided in most cases.

**Signature**
```javascript
useImperativeHandle(ref, create, deps?);
```

**Parameters**

<x-field data-name="ref" data-type="React.Ref<T>" data-required="true" data-desc="The ref passed down from a parent component, typically from `forwardRef`."></x-field>

<x-field data-name="create" data-type="() => T" data-required="true" data-desc="A function that returns the value that the ref should expose. This value can be an object with custom methods."></x-field>

<x-field data-name="deps" data-type="Array<mixed>" data-required="false" data-desc="An optional dependency array. The `create` function will be re-invoked only when one of the dependencies has changed."></x-field>

## Other Hooks

This section covers the remaining built-in Hooks.

| Hook | Description |
|---|---|
| `useContext` | Accepts a context object and returns the current context value. |
| `useMemo` | Returns a memoized value, recomputing it only when dependencies change. |
| `useCallback` | Returns a memoized callback, creating a new function only when dependencies change. |
| `useDebugValue` | Displays a label for custom hooks in React DevTools. |
| `useTransition` | Returns a stateful value for the pending state of a transition, and a function to start it. |
| `useDeferredValue` | Accepts a value and returns a new copy of the value that will defer to more urgent updates. |
| `useId` | Generates unique IDs that are stable across the server and client. |
| `useSyncExternalStore` | Recommends for reading and subscribing from external data sources in a way that’s compatible with concurrent rendering features. |

For more advanced use cases and experimental features, you might explore APIs like `use` for reading promise or context values. Remember to consult the [Advanced Guides](./advanced-guides.md) for cutting-edge functionality.

After mastering Hooks, you may want to learn more about [Top-Level APIs](./api-reference-top-level.md) like `memo` and `lazy` that help you further optimize your components.
