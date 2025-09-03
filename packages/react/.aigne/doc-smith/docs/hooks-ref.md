# Ref Hooks

Ref Hooks provide a way to access DOM nodes or persist mutable values across renders without causing a re-render. They are essential for managing values that are not directly tied to the component's visual output, such as managing focus, media playback, or integrating with third-party DOM libraries. This section covers `useRef` and `useImperativeHandle`.

For managing component state that triggers re-renders, see [State Hooks](./hooks-state.md).

## `useRef`

The `useRef` Hook returns a mutable ref object whose `.current` property is initialized to the passed argument (`initialValue`). The returned object will persist for the full lifetime of the component. A common use case is to get direct access to a DOM element.

Changing the `.current` property of a ref does **not** cause a re-render.

### Syntax

```javascript
function useRef<T>(initialValue: T): {current: T}
```

### Parameters

| Name           | Type | Description                                        |
|----------------|------|----------------------------------------------------|
| `initialValue` | `T`  | The value you want the `ref` object's `current` property to be initially. It can be a value of any type. |

### Returns

`useRef` returns a single, mutable object with a property named `current`.

### Example: Accessing a DOM Element

Here is an example of using `useRef` to store a reference to an input DOM element and focus it when a button is clicked.

```javascript
import { useRef } from 'react';

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

In this example, `inputEl` holds the reference to the `<input>` element. When the button is clicked, we call the `focus()` method on `inputEl.current`.

## `useImperativeHandle`

`useImperativeHandle` customizes the instance value that is exposed to parent components when using `ref`. This is useful when you want to control what a parent component can access on a child component's instance. It should always be used in conjunction with `forwardRef`.

This hook allows you to expose a specific, limited API to the parent instead of the entire component instance, which can help in creating more predictable and decoupled components.

### Syntax

```javascript
function useImperativeHandle<T>(
  ref: {current: T | null} | ((inst: T | null) => mixed) | null | void,
  create: () => T,
  deps: Array<mixed> | void | null,
): void
```

### Parameters

| Name     | Type          | Description                                                                                                                                                              |
|----------|---------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ref`    | `RefObject`   | The `ref` forwarded from the parent component via `forwardRef`.                                                                                                          |
| `create` | `() => T`     | A function that returns the value to be exposed as the ref's `current` value. This value can be an object with methods or properties you want to expose.                       |
| `deps`   | `Array<any>?` | An optional array of dependencies. The `create` function will be re-executed whenever any of the dependencies in this array change. If omitted, it runs on every render. |


### Example: Exposing a Custom Focus Method

In this example, a parent component needs to focus an input inside a child component. The child component `FancyInput` uses `useImperativeHandle` to expose only a `focus` method.

```javascript
import React, { useRef, useImperativeHandle, forwardRef } from 'react';

// Child component using forwardRef and useImperativeHandle
const FancyInput = forwardRef((props, ref) => {
  const inputRef = useRef();
  
  useImperativeHandle(ref, () => ({
    // Expose a custom 'focus' method to the parent
    focus: () => {
      if (inputRef.current) {
        inputRef.current.focus();
      }
    },
    // You could expose other methods here as well
    clear: () => {
      if (inputRef.current) {
        inputRef.current.value = '';
      }
    }
  }));

  return <input ref={inputRef} type="text" placeholder="Enter text..." />;
});

// Parent component
function App() {
  const fancyInputRef = useRef(null);

  const handleFocusClick = () => {
    if (fancyInputRef.current) {
      fancyInputRef.current.focus();
    }
  };

  return (
    <div>
      <FancyInput ref={fancyInputRef} />
      <button onClick={handleFocusClick}>
        Focus Child Input
      </button>
    </div>
  );
}
```

The parent component can now call `fancyInputRef.current.focus()` but cannot directly access the underlying `<input>` DOM node or other internal properties of `FancyInput`.

---

By using `useRef` and `useImperativeHandle`, you can effectively manage interactions that fall outside the standard declarative data flow of React. 

Next, learn how to optimize your application's rendering performance with [Performance Hooks](./hooks-performance.md).