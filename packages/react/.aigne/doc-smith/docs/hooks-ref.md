# Ref Hooks

Ref Hooks provide a way to access DOM nodes directly or to create a reference to a value that persists across renders without causing a re-render itself. They are essential for managing focus, media playback, or integrating with third-party DOM libraries.

This section covers `useRef` for creating a general-purpose ref and `useImperativeHandle` for customizing the ref exposed by a component. For a foundational understanding of refs, you might want to review the [Refs](./core-apis-refs.md) documentation first.

## `useRef`

The `useRef` Hook returns a mutable ref object whose `.current` property is initialized to the passed argument (`initialValue`). The returned object will persist for the full lifetime of the component.

There are two primary use cases for `useRef`:
1.  Accessing a DOM element.
2.  Holding a mutable value that does not trigger a re-render when it changes.

**Signature**

```typescript
function useRef<T>(initialValue: T): {current: T};
```

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| `initialValue` | `T` | The initial value for the ref's `current` property. It's only used on the initial render. |

**Returns**

A mutable ref object with a single property:

| Name | Type | Description |
| --- | --- | --- |
| `current` | `T` | Initially set to `initialValue`. You can mutate this property directly. Changing it does not cause a component re-render. |

### Example: Accessing a DOM Element

This is the most common use case for `useRef`. You can pass the ref object to the `ref` attribute of a JSX element to get a direct reference to its underlying DOM node.

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

In this example, `inputEl.current` will be the `<input>` DOM node after React mounts it. Clicking the button calls the DOM node's `focus()` method.

### Example: Storing a Mutable Value

You can also use `useRef` to store any mutable value, similar to an instance property on a class. This is useful for values you want to persist across renders but don't want to trigger re-renders when they change, such as a timer ID.

```javascript
import React, { useRef, useEffect } from 'react';

function Timer() {
  const intervalRef = useRef(null);

  useEffect(() => {
    intervalRef.current = setInterval(() => {
      console.log('Timer tick');
    }, 1000);

    // Cleanup function
    return () => {
      if (intervalRef.current) {
        clearInterval(intervalRef.current);
      }
    };
  }, []); // Empty dependency array means this effect runs once on mount

  return <div>Timer is running (check console)</div>;
}
```

Here, `intervalRef` holds the ID returned by `setInterval`. We can access it in the cleanup function to clear the interval when the component unmounts, without causing any re-renders when `intervalRef.current` is assigned.

## `useImperativeHandle`

The `useImperativeHandle` Hook customizes the instance value that is exposed to parent components when using `ref`. It should be used in conjunction with `forwardRef`.

Instead of exposing the entire component instance, `useImperativeHandle` allows you to expose a specific, limited set of imperative methods. This helps to avoid breaking encapsulation by preventing parent components from depending on the child's internal DOM structure.

**Signature**

```typescript
function useImperativeHandle<T>(
  ref: {current: T | null} | ((inst: T | null) => mixed) | null | void,
  create: () => T,
  deps: Array<mixed> | void | null,
): void;
```

**Parameters**

| Name | Type | Description |
| --- | --- | --- |
| `ref` | `Ref<T>` | The `ref` forwarded from the parent component via `forwardRef`. |
| `create` | `() => T` | A function that returns the value to be exposed. This value will be set as the `current` value of the parent's ref. |
| `deps` | `Array<mixed>` | An optional dependency array. The `create` function will be re-executed whenever a value in this array changes. |

### Example: Exposing a Custom `focus` Method

Below is an example of a `FancyInput` component that uses `useImperativeHandle` to expose only a `focus` method to its parent.

```javascript
import React, { useRef, useImperativeHandle, forwardRef } from 'react';

// Child component
const FancyInput = forwardRef((props, ref) => {
  const inputRef = useRef();
  
  useImperativeHandle(ref, () => ({
    // Expose a custom `focus` method
    focus: () => {
      if (inputRef.current) {
        inputRef.current.focus();
        console.log('Child input focused imperatively!');
      }
    }
  }));

  return <input ref={inputRef} placeholder="I am a fancy input" />;
});

// Parent component
function App() {
  const fancyInputRef = useRef();

  const handleClick = () => {
    if (fancyInputRef.current) {
      fancyInputRef.current.focus();
    }
  };

  return (
    <div>
      <FancyInput ref={fancyInputRef} />
      <button onClick={handleClick}>
        Focus Child Input
      </button>
    </div>
  );
}
```

The `App` component can now call `fancyInputRef.current.focus()`, but it cannot directly access the underlying `<input>` DOM node, preserving the encapsulation of `FancyInput`.

### How Parent and Child Refs Interact

The following diagram illustrates the flow of control when a parent component calls an imperative method on a child component that uses `useImperativeHandle`.

```d2
direction: down

Parent-Component: {
  shape: rectangle
  
  Child-Ref: {
    label: "const childRef = useRef()"
    shape: oval
  }
  
  Rendered-Child: {
    label: "<FancyInput ref={childRef} />"
    shape: rectangle
  }

  Button: {
    label: "<button onClick={() => childRef.current.focus()} />"
    shape: rectangle
  }
}

FancyInput-Component: {
  label: "FancyInput (wrapped in forwardRef)"
  shape: package

  useImperativeHandle: {
    label: "useImperativeHandle(ref, () => ({ focus: ... }))"
    shape: hexagon
  }

  Internal-Input: {
    label: "<input ref={internalInputRef} />"
    shape: rectangle
  }
}

Parent-Component.Button -> Parent-Component.Child-Ref: "1. onClick triggers call"
Parent-Component.Child-Ref -> FancyInput-Component.useImperativeHandle: "2. Accesses exposed 'focus' method"
FancyInput-Component.useImperativeHandle -> FancyInput-Component.Internal-Input: "3. Manipulates internal DOM node"

```

By using `useRef` and `useImperativeHandle`, you can manage interactions that fall outside the typical top-down data flow of React, while still keeping component APIs clean and predictable.

---

With refs covered, you now have the tools to handle direct DOM manipulation and persistent mutable state. Next, let's explore how to optimize your application's rendering performance.

Next up: [Performance Hooks](./hooks-performance.md)
