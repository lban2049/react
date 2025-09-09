# Effect Hooks

Effect Hooks allow you to perform side effects in function components. Side effects are operations that can affect other components and can't be done during rendering, such as data fetching, setting up a subscription, or manually changing the DOM.

React provides three primary hooks to manage effects, each serving a different purpose based on its timing within the render cycle.

<x-cards data-columns="3">
  <x-card data-title="useEffect" data-icon="lucide:play-circle">
    The most common hook for side effects. It runs asynchronously after the render is committed to the screen, so it doesn't block painting.
  </x-card>
  <x-card data-title="useLayoutEffect" data-icon="lucide:ruler">
    Fires synchronously after all DOM mutations but before the browser paints. Ideal for reading layout from the DOM and synchronously re-rendering.
  </x-card>
  <x-card data-title="useInsertionEffect" data-icon="lucide:syringe">
    Runs synchronously before React makes changes to the DOM. This is for very specific use cases, like CSS-in-JS libraries injecting styles.
  </x-card>
</x-cards>

---

## useEffect

`useEffect` is the go-to hook for most side effects. By deferring its execution until after the browser has painted, it ensures that your effects don't slow down the user experience.

The function you pass to `useEffect` is the effect itself. You can optionally return a "cleanup" function from your effect, which React will run when the component unmounts or before the effect re-runs.

### Parameters

| Parameter | Type | Description |
|---|---|---|
| `create` | `() => (() => void) \| void` | A function that contains the side-effect logic. It can optionally return a cleanup function. |
| `deps` | `Array<mixed> \| void \| null` | An array of dependencies. The effect will only re-run if one of these dependencies has changed since the last render. If omitted, the effect runs after every render. If an empty array `[]` is passed, the effect runs only once after the initial render. |

### Example: Fetching Data

This example shows how to fetch data from an API and update the component's state. The effect re-runs whenever the `userId` prop changes.

```javascript Fetching Data with useEffect icon=logos:javascript
import React, { useState, useEffect } from 'react';

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    setLoading(true);
    const fetchUserData = async () => {
      const response = await fetch(`https://api.example.com/users/${userId}`);
      const data = await response.json();
      setUser(data);
      setLoading(false);
    };

    fetchUserData();

    // The dependency array [userId] ensures this effect runs
    // only when the userId prop changes.
  }, [userId]);

  if (loading) {
    return <div>Loading...</div>;
  }

  return <div>{user.name}</div>;
}
```

### Example: Subscribing with Cleanup

This example shows how to subscribe to a browser event and use the cleanup function to unsubscribe, preventing memory leaks.

```javascript Event Listener with Cleanup icon=logos:javascript
import React, { useState, useEffect } from 'react';

function MousePosition() {
  const [position, setPosition] = useState({ x: 0, y: 0 });

  useEffect(() => {
    const handleMouseMove = (e) => {
      setPosition({ x: e.clientX, y: e.clientY });
    };

    window.addEventListener('mousemove', handleMouseMove);

    // Return a cleanup function
    return () => {
      window.removeEventListener('mousemove', handleMouseMove);
    };
  }, []); // Empty array means this effect runs once on mount and cleans up on unmount

  return (
    <p>
      Mouse X: {position.x}, Y: {position.y}
    </p>
  );
}
```

---

## useLayoutEffect

`useLayoutEffect` has the same signature as `useEffect`, but it fires synchronously after all DOM mutations are complete and before the browser paints the changes. This makes it suitable for tasks that need to read layout from the DOM (like getting an element's dimensions) and then trigger a synchronous re-render before the user sees any visual inconsistencies.

**Warning:** Since `useLayoutEffect` is synchronous, it can block visual updates. Prefer `useEffect` when possible to avoid performance penalties.

### Parameters

| Parameter | Type | Description |
|---|---|---|
| `create` | `() => (() => void) \| void` | A function that contains the side-effect logic. It can optionally return a cleanup function. |
| `deps` | `Array<mixed> \| void \| null` | An array of dependencies that controls when the effect re-runs. |

### Example: Measuring a DOM Node

Imagine you need to position a tooltip based on a button's width. `useLayoutEffect` ensures that you measure the button after it has been rendered but before the user sees it, preventing a flicker where the tooltip might briefly appear in the wrong place.

```javascript Positioning a Tooltip icon=logos:javascript
import React, { useState, useLayoutEffect, useRef } from 'react';

function TooltipButton() {
  const buttonRef = useRef(null);
  const [tooltipWidth, setTooltipWidth] = useState(0);

  useLayoutEffect(() => {
    if (buttonRef.current) {
      // Read the button's width from the DOM
      const width = buttonRef.current.offsetWidth;
      // Update state synchronously before the browser paints
      setTooltipWidth(width);
    }
  }, []); // Run only once after the initial layout

  return (
    <div>
      <button ref={buttonRef}>Hover over me</button>
      <div style={{ width: tooltipWidth, border: '1px solid black', marginTop: '5px' }}>
        This tooltip has the same width as the button.
      </div>
    </div>
  );
}
```

---

## useInsertionEffect

`useInsertionEffect` is a specialized hook that runs synchronously *before* any DOM mutations. Its primary use case is for CSS-in-JS libraries to inject style rules into the DOM before the browser performs layout calculations. This ensures that style definitions are available when React components are rendered, preventing style-related flickers.

**Note:** Application developers will rarely need to use this hook. It's intended for library authors. You cannot access DOM `ref`s inside `useInsertionEffect`.

### Parameters

| Parameter | Type | Description |
|---|---|---|
| `create` | `() => (() => void) \| void` | A function that contains the style insertion logic. It can optionally return a cleanup function. |
| `deps` | `Array<mixed> \| void \| null` | An array of dependencies that controls when the effect re-runs. |

### Example: Conceptual Style Injection

This example illustrates how a CSS-in-JS library might use `useInsertionEffect` to inject a `<style>` tag into the document's `<head>`.

```javascript Conceptual CSS-in-JS Usage icon=logos:javascript
import { useInsertionEffect } from 'react';

let injectedRules = new Set();

function useCSS(rule) {
  useInsertionEffect(() => {
    // This runs before the browser has a chance to see the DOM updates.
    if (!injectedRules.has(rule)) {
      injectedRules.add(rule);
      const styleElement = document.createElement('style');
      styleElement.innerHTML = rule;
      document.head.appendChild(styleElement);

      // Optional: Return a cleanup function to remove the style
      return () => {
        document.head.removeChild(styleElement);
        injectedRules.delete(rule);
      };
    }
  }, [rule]);
}

function MyStyledComponent() {
  // A library would abstract this away
  useCSS('.my-class { color: blue; }');
  return <div className="my-class">This is a styled component.</div>;
}
```

---

Now that you understand how to handle side effects, you might need to directly interact with DOM nodes or forward refs to child components. Learn how in the next section.

➡️ [Next: Ref Hooks](./hooks-ref.md)
