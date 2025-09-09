# Ref Hooks

Ref Hooks provide a way to access DOM nodes directly or to keep a mutable value that doesn't trigger a re-render upon mutation. They are essential for integrating with third-party libraries, managing focus, or storing values that persist for the full lifetime of the component without being part of the rendering logic.

This section covers the two primary Ref Hooks:

<x-cards>
  <x-card data-title="useRef" data-icon="lucide:mouse-pointer-square">
    Creates a mutable ref object whose `.current` property can hold a value, often a reference to a DOM node.
  </x-card>
  <x-card data-title="useImperativeHandle" data-icon="lucide:hand-pointing">
    Customizes the instance value that is exposed to parent components when using `ref` with `forwardRef`.
  </x-card>
</x-cards>

---

## `useRef`

The `useRef` Hook returns a mutable ref object. This object has a single property, `current`, which you can set to any value. `useRef` is useful for two main scenarios: accessing DOM elements and storing mutable values that don't cause re-renders.

### Syntax

```javascript useRef Hook Signature icon=logos:javascript
function useRef<T>(initialValue: T): {current: T};
```

### Parameters

| Parameter | Type | Description |
|---|---|---|
| `initialValue` | `T` | The value you want the ref object’s `current` property to be initialized to. It can be a value of any type. |

### Returns

`useRef` returns a single object with a `current` property. Initially, `current` is set to the `initialValue` you provided. You can later set it to something else. If you pass the ref object to a JSX node's `ref` attribute, React will put the corresponding DOM node into its `current` property.

### Example 1: Accessing a DOM Element

A common use case for `useRef` is to get direct access to a DOM element, allowing you to call imperative methods on it, such as `focus()`.

```javascript Focus an Input with useRef icon=logos:javascript
import React, { useRef } from 'react';

function FocusInput() {
  const inputRef = useRef(null);

  const handleFocusClick = () => {
    // Access the DOM node directly via inputRef.current
    if (inputRef.current) {
      inputRef.current.focus();
    }
  };

  return (
    <>
      <input ref={inputRef} type="text" placeholder="Click button to focus" />
      <button onClick={handleFocusClick}>Focus the input</button>
    </>
  );
}

export default FocusInput;
```
In this example, `inputRef` is attached to the `<input>` element. When the button is clicked, `inputRef.current` holds the actual DOM node, and we can call its `focus()` method.

### Example 2: Storing a Mutable Value

You can also use `useRef` to hold any mutable value, similar to an instance variable in a class. The key difference from state is that updating a ref does not trigger a component re-render.

```javascript Storing an Interval ID icon=logos:javascript
import React, { useState, useRef, useEffect } from 'react';

function Timer() {
  const [count, setCount] = useState(0);
  const intervalRef = useRef(null);

  useEffect(() => {
    // Start the interval
    intervalRef.current = setInterval(() => {
      setCount(prevCount => prevCount + 1);
    }, 1000);

    // Clean up the interval on component unmount
    return () => {
      clearInterval(intervalRef.current);
    };
  }, []); // Empty dependency array means this effect runs only once on mount

  const handleStopTimer = () => {
    clearInterval(intervalRef.current);
  };

  return (
    <div>
      <p>Timer: {count} seconds</p>
      <button onClick={handleStopTimer}>Stop Timer</button>
    </div>
  );
}

export default Timer;
```
Here, `intervalRef` stores the ID returned by `setInterval`. We can access this ID later in the `handleStopTimer` function or the `useEffect` cleanup function to clear the interval without causing the component to re-render every time the ID is set.

---

## `useImperativeHandle`

`useImperativeHandle` lets you customize the ref handle exposed by a child component. Instead of exposing the entire DOM node, you can define a specific set of imperative functions for the parent component to call. This hook should be used with `forwardRef`.

### Syntax

```javascript useImperativeHandle Hook Signature icon=logos:javascript
function useImperativeHandle<T>(
  ref: {current: T | null} | ((inst: T | null) => mixed) | null | void,
  create: () => T,
  deps: Array<mixed> | void | null,
): void;
```

### Parameters

| Parameter | Type | Description |
|---|---|---|
| `ref` | `RefObject` | The `ref` passed down from the parent component through `forwardRef`. |
| `create` | `() => T` | A function that returns the custom handle. This handle can be an object with methods and properties. |
| `deps` | `Array<any>` | (Optional) A dependency array. The `create` function is re-executed whenever a value in this array changes. |

### Example: Exposing a Custom API

This example shows a custom input component that exposes only `focus` and `clear` methods to its parent, hiding the underlying `input` element implementation.

```javascript CustomInput Component icon=logos:javascript
import React, { useRef, useImperativeHandle, forwardRef } from 'react';

const CustomInput = forwardRef((props, ref) => {
  const internalInputRef = useRef(null);

  // Expose a custom handle to the parent component
  useImperativeHandle(ref, () => ({
    focus: () => {
      internalInputRef.current.focus();
    },
    clear: () => {
      internalInputRef.current.value = '';
    }
  }));

  return <input ref={internalInputRef} {...props} />;
});

// Parent Component
function App() {
  const customInputRef = useRef(null);

  const handleFocus = () => {
    customInputRef.current.focus();
  };

  const handleClear = () => {
    customInputRef.current.clear();
  };

  return (
    <div>
      <CustomInput ref={customInputRef} placeholder="I have a custom API" />
      <button onClick={handleFocus}>Focus Input</button>
      <button onClick={handleClear}>Clear Input</button>
    </div>
  );
}

export default App;
```
In the `App` component, `customInputRef.current` does not point to the DOM `<input>` element. Instead, it points to the object `{ focus: () => {..}, clear: () => {..} }` defined in `useImperativeHandle`.

## Summary

Ref Hooks are a powerful tool for breaking out of the standard declarative rendering flow when necessary. 

- Use `useRef` to access DOM elements or to keep mutable data that doesn't affect rendering.
- Use `useImperativeHandle` with `forwardRef` to create a clean and limited imperative API for your components, hiding implementation details from the parent.

Now that you understand how to manage refs, you can explore ways to optimize your application's performance. Learn more in the [Performance Hooks](./hooks-performance.md) section.