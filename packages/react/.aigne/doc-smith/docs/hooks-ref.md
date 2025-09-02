# Ref Hooks

Ref Hooks provide a way to access DOM nodes directly or to persist mutable values across renders without causing a re-render. They are essential for integrating with third-party DOM libraries, managing focus, and handling values that don't directly participate in the component's visual output.

For a foundational understanding of refs, you might want to review the [Refs](./core-apis-refs.md) documentation first.

This section covers the following hooks:
- `useRef`: Creates a mutable ref object that can hold a value or a DOM node.
- `useImperativeHandle`: Customizes the instance value exposed to parent components when using `ref`.

---

## useRef

The `useRef` Hook returns a mutable ref object whose `.current` property is initialized to the passed argument (`initialValue`). The returned object will persist for the full lifetime of the component.

A common use case for `useRef` is to get direct access to a DOM element. It can also be used to hold any mutable value, similar to how you would use instance fields in a class.

```d2
direction: down

Component: {
  "useRef(initialValue)": {
    style.bold: true
  }

  "Ref Object": {
    shape: cylinder
    ".current": "initialValue"
  }

  "Component Lifetime": {
    shape: sequence_diagram
    A: "Mount"
    B: "Re-render"
    C: "Re-render"
    D: "Unmount"

    A -> B -> C -> D
  }

  "useRef(initialValue)" -> "Ref Object": "Returns"
  "Ref Object" -> "Component Lifetime": "Persists across renders"
}
```

### Syntax

```javascript
export function useRef<T>(initialValue: T): {current: T}
```

### Parameters

| Parameter      | Type | Description                                                      |
| -------------- | ---- | ---------------------------------------------------------------- |
| `initialValue` | `T`  | The initial value for the `current` property of the ref object. |

### Returns

`useRef` returns a single, mutable object with a `current` property. Initially, `ref.current` is set to `initialValue`. You can mutate `ref.current` later. If you pass the ref object to React as a `ref` attribute on a JSX node (e.g., `<div ref={myRef} />`), React will set its `current` property to the corresponding DOM node.

### Example 1: Accessing a DOM Element

You can use a ref to manipulate a DOM element directly, such as managing focus, text selection, or media playback.

```javascript
import React, { useRef } from 'react';

function TextInputWithFocusButton() {
  const inputEl = useRef(null);

  const onButtonClick = () => {
    // `current` points to the mounted text input element
    if (inputEl.current) {
      inputEl.current.focus();
    }
  };

  return (
    <>
      <input ref={inputEl} type="text" />
      <button onClick={onButtonClick}>Focus the input</button>
    </>
  );
}
```

In this example, `useRef` creates a ref object. We attach it to the `<input>` element by passing `<input ref={inputEl}>`. When the button is clicked, `inputEl.current.focus()` is called, which programmatically focuses the input field.

### Example 2: Storing a Mutable Value

`useRef` is also useful for keeping any mutable value around. This is handy for values that you want to persist across renders but don't want to trigger a re-render when they change.

```javascript
import React, { useState, useEffect, useRef } from 'react';

function Timer() {
  const [count, setCount] = useState(0);
  const intervalRef = useRef(null);

  useEffect(() => {
    intervalRef.current = setInterval(() => {
      setCount(prevCount => prevCount + 1);
    }, 1000);

    // Cleanup function
    return () => {
      clearInterval(intervalRef.current);
    };
  }, []); // Empty dependency array means this effect runs only once on mount

  return (
    <div>
      <h1>Count: {count}</h1>
      <button onClick={() => clearInterval(intervalRef.current)}>
        Stop Timer
      </button>
    </div>
  );
}
```

Here, `useRef` stores the interval ID. We can access and clear this interval from anywhere in the component without causing re-renders, as changing `intervalRef.current` does not trigger a component update.

---

## useImperativeHandle

`useImperativeHandle` customizes the instance value that is exposed to parent components when using `ref`. It should always be used in conjunction with `forwardRef`. This allows a child component to expose specific functions to its parent, rather than exposing the entire component instance, providing better encapsulation.

### Syntax

```javascript
export function useImperativeHandle<T>(
  ref: {current: T | null} | ((inst: T | null) => mixed) | null | void,
  create: () => T,
  deps: Array<mixed> | void | null,
): void
```

### Parameters

| Parameter | Type          | Description                                                                                                   |
| --------- | ------------- | ------------------------------------------------------------------------------------------------------------- |
| `ref`     | `Ref<T>`      | The ref forwarded from the parent component.                                                                  |
| `create`  | `() => T`     | A function that returns the custom handle. This handle will be assigned to the `ref.current` property in the parent. |
| `deps`    | `Array<any>`  | An optional dependency array. The `create` function is re-executed whenever one of the dependencies has changed. |

### Example

In this example, a parent component needs to call a `focusAndClear` method on a custom input component.

```javascript
import React, { useRef, useImperativeHandle, forwardRef, useState } from 'react';

// Child component
const CustomInput = forwardRef((props, ref) => {
  const [value, setValue] = useState('');
  const inputRef = useRef();

  useImperativeHandle(ref, () => ({
    focusAndClear: () => {
      inputRef.current.focus();
      setValue('');
    },
    // You can expose other methods here
  }));

  return (
    <input
      ref={inputRef}
      value={value}
      onChange={(e) => setValue(e.target.value)}
      placeholder="Type something..."
    />
  );
});

// Parent component
function ParentComponent() {
  const customInputRef = useRef(null);

  const handleFocusAndClear = () => {
    if (customInputRef.current) {
      customInputRef.current.focusAndClear();
    }
  };

  return (
    <div>
      <CustomInput ref={customInputRef} />
      <button onClick={handleFocusAndClear}>Focus and Clear Input</button>
    </div>
  );
}
```

The `ParentComponent` creates a ref (`customInputRef`) and passes it to `CustomInput`. The `CustomInput` component, wrapped in `forwardRef`, uses `useImperativeHandle` to define exactly which properties and methods are accessible on `customInputRef.current`. The parent can then call `customInputRef.current.focusAndClear()` to interact with the child component in a controlled way.

---

With Ref Hooks, you have precise control over DOM elements and mutable state that doesn't affect rendering.

Next, let's explore how to optimize your application with [Performance Hooks](./hooks-performance.md).