# Effect Hooks

Effect Hooks allow you to perform side effects in function components. Side effects are operations that can affect other components and can't be done during rendering, such as data fetching, setting up a subscription, or manually changing the DOM.

This section covers the three primary effect hooks provided by React, each designed for a different timing within the component lifecycle: `useEffect`, `useLayoutEffect`, and `useInsertionEffect`.

## useEffect

The `useEffect` hook is the most common tool for handling side effects. It runs asynchronously *after* the render is committed to the screen and the browser has painted the result. This ensures that your effect code doesn't block the browser from updating the display.

### Syntax

```javascript
useEffect(
  create: () => (() => void) | void,
  deps: Array<mixed> | void | null,
): void
```

### Parameters

| Name     | Type                | Description                                                                                                                                                                                                                                                        | 
| :------- | :------------------ | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | 
| `create`   | `function`          | A function that contains the side effect logic. It can optionally return a cleanup function, which React will run when the component unmounts or before the effect runs again.                                                                                           | 
| `deps`     | `Array<any>` (optional) | An array of dependencies. The effect will re-run only if one of these dependencies has changed since the last render. If omitted, the effect runs after every render. If an empty array `[]` is provided, the effect runs only once after the initial render. | 

### Example: Fetching Data

Here's an example of using `useEffect` to fetch data from an API when the component mounts.

```javascript
import React, { useState, useEffect } from 'react';

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    // This function runs after the component renders
    const fetchUserData = async () => {
      const response = await fetch(`https://api.example.com/users/${userId}`);
      const data = await response.json();
      setUser(data);
    };

    fetchUserData();

    // Optional cleanup function
    return () => {
      // This code would run if the component unmounts or if userId changes.
      // For example, you could cancel a pending API request here.
      console.log(`Cleaning up effect for user ${userId}`);
    };
  }, [userId]); // The effect re-runs only if userId changes

  if (!user) {
    return <div>Loading...</div>;
  }

  return <div>{user.name}</div>;
}
```

## useLayoutEffect

`useLayoutEffect` has the same signature as `useEffect`, but it fires synchronously *after* all DOM mutations have been applied, but *before* the browser has a chance to paint the changes. This is useful for reading layout from the DOM (like getting an element's size or position) and synchronously re-rendering to apply changes before the user sees them.

Use this hook with caution, as synchronous execution can block visual updates.

### Syntax

```javascript
useLayoutEffect(
  create: () => (() => void) | void,
  deps: Array<mixed> | void | null,
): void
```

### Example: Measuring a DOM Element

Imagine you need to position a tooltip based on the dimensions of a button. `useLayoutEffect` ensures you measure the button after it has been rendered but before it's painted, preventing any visual flicker.

```javascript
import React, { useState, useLayoutEffect, useRef } from 'react';

function Tooltip() {
  const buttonRef = useRef(null);
  const [tooltipWidth, setTooltipWidth] = useState(0);

  useLayoutEffect(() => {
    if (buttonRef.current) {
      // Measure the button's width after it's in the DOM but before paint
      const width = buttonRef.current.offsetWidth;
      setTooltipWidth(width);
    }
  }, []); // Runs only once after initial layout

  return (
    <div>
      <button ref={buttonRef}>Hover over me</button>
      <div style={{ width: tooltipWidth, marginTop: '10px', border: '1px solid black' }}>
        This tooltip has the same width as the button.
      </div>
    </div>
  );
}
```

## useInsertionEffect

`useInsertionEffect` is a specialized hook that runs synchronously *before* any DOM mutations are made. Its primary use case is for CSS-in-JS libraries to inject dynamic styles into the DOM. Application developers will rarely need to use this hook.

Since it runs before DOM updates, it is the ideal place to inject `<style>` tags without forcing the browser to recalculate styles multiple times in one render cycle.

### Syntax

```javascript
useInsertionEffect(
  create: () => (() => void) | void,
  deps: Array<mixed> | void | null,
): void
```

### Conceptual Example

This is a simplified example of how a styling library might use `useInsertionEffect`.

```javascript
// Inside a hypothetical CSS-in-JS library
let ruleCache = new Set();

function useCSS(rule) {
  useInsertionEffect(() => {
    // Inject styles before the browser sees the new DOM nodes.
    if (!ruleCache.has(rule)) {
      const styleElement = document.createElement('style');
      styleElement.innerHTML = rule;
      document.head.appendChild(styleElement);
      ruleCache.add(rule);
    }
  }, [rule]);
}

// Application code using the library
function MyComponent() {
  useCSS(`.my-component { color: blue; }`);
  return <div className="my-component">This is blue</div>;
}
```

## Comparison and Execution Order

The key difference between these hooks is their timing within the React render-commit lifecycle. The following diagram illustrates the sequence of events.

```d2
direction: down

"Render Phase": {
  shape: rectangle
  label: "React renders your components"
}

"Commit Phase": {
  shape: package
  grid-columns: 1
  style.stroke-dash: 2

  "1. Before DOM Mutations": {
    shape: rectangle
    
    "useInsertionEffect runs": {
      shape: oval
      style.fill: "#fffbe6"
    }
  }

  "2. DOM Mutations & Layout Calculation": {
    shape: rectangle

    "useLayoutEffect runs": {
      shape: oval
      style.fill: "#e6f7ff"
    }
  }

  "3. Browser Painting": {
    shape: rectangle
    label: "Browser paints the screen"
  }
  
  "4. After Paint": {
    shape: rectangle
    
    "useEffect runs": {
      shape: oval
      style.fill: "#f6ffed"
    }
  }
}

"Render Phase" -> "Commit Phase": "Triggers commit"
"Commit Phase"."1. Before DOM Mutations" -> "Commit Phase"."2. DOM Mutations & Layout Calculation"
"Commit Phase"."2. DOM Mutations & Layout Calculation" -> "Commit Phase"."3. Browser Painting"
"Commit Phase"."3. Browser Painting" -> "Commit Phase"."4. After Paint"

```

| Hook                 | Timing                                                    | Use Case                                                    |
| -------------------- | --------------------------------------------------------- | ----------------------------------------------------------- |
| `useInsertionEffect` | Synchronous, before DOM mutations                         | Injecting styles for CSS-in-JS libraries.                   |
| `useLayoutEffect`    | Synchronous, after DOM mutations, before browser paint    | Measuring DOM elements, synchronous re-renders.             |
| `useEffect`          | Asynchronous, after render is committed and browser paint | Data fetching, subscriptions, and most other side effects.  |

---

Understanding the different Effect Hooks allows you to manage side effects precisely and efficiently. To learn how to interact directly with DOM nodes or component instances, proceed to the next section on [Ref Hooks](./hooks-ref.md).
