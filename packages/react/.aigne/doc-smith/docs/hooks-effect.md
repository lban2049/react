# Effect Hooks

Effect Hooks allow components to perform side effects, enabling them to interact with and synchronize with systems outside of React's control. Side effects include actions like fetching data, setting up subscriptions, or manually manipulating the DOM.

React provides three primary hooks for handling side effects, each running at a different point in the component lifecycle to address specific use cases.

```d2
direction: down

Render-Phase: {
  label: "Render Phase"
  shape: rectangle
  "1. React renders your component"
}

Commit-Phase: {
  label: "Commit Phase"
  shape: rectangle
  grid-columns: 1

  "2. React commits changes to the DOM"

  useInsertionEffect-run: {
    label: "3. `useInsertionEffect` runs\n(For CSS-in-JS libraries)"
  }

  useLayoutEffect-run: {
    label: "4. `useLayoutEffect` runs\n(Reads layout before paint)"
  }
}

Browser-Paint: {
  label: "Browser Paint"
  shape: rectangle
  "5. Browser paints the screen"
}

useEffect-Execution: {
  label: "useEffect Execution"
  shape: rectangle
  "6. `useEffect` runs\n(For most side effects)"
}

Render-Phase -> Commit-Phase: "Triggers"
Commit-Phase -> Browser-Paint: "Synchronous"
Browser-Paint -> useEffect-Execution: "Asynchronous"
```

---

## `useEffect`

The `useEffect` hook is the most common tool for handling side effects. It runs *after* React has rendered the component and the browser has painted the screen, ensuring that the effect code does not block the visual update.

**Signature**

```javascript
useEffect(
  create: () => (() => void) | void,
  deps: Array<mixed> | void | null,
): void
```

**Parameters**

| Parameter | Type | Description |
|---|---|---|
| `create` | `function` | A function that contains the side-effect logic. It can optionally return a cleanup function. |
| `deps` | `Array<mixed>` \| `null` \| `undefined` | An array of dependencies. The effect will re-run only if one of these dependencies has changed since the last render. If omitted, the effect runs after every render. If an empty array `[]` is provided, the effect runs only once. |

**Usage**

`useEffect` is ideal for data fetching, setting up event listeners, or any other asynchronous operation.

**Example: Fetching Data**

```javascript
import React, { useState, useEffect } from 'react';

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    // This function contains the side effect.
    async function fetchUserData() {
      const response = await fetch(`https://api.example.com/users/${userId}`);
      const data = await response.json();
      setUser(data);
    }

    fetchUserData();

    // Optional: Return a cleanup function.
    return () => {
      // This code runs when the component unmounts or before the effect re-runs.
      // For example, to cancel an ongoing request.
      console.log(`Cleaning up effect for user ${userId}`);
    };
  }, [userId]); // The effect re-runs only if userId changes.

  if (!user) {
    return <div>Loading...</div>;
  }

  return <h1>{user.name}</h1>;
}
```

---

## `useLayoutEffect`

`useLayoutEffect` has the same signature as `useEffect`, but it fires synchronously *after* all DOM mutations and *before* the browser repaints the screen. This is useful for reading layout from the DOM and synchronously re-rendering.

**Signature**

```javascript
useLayoutEffect(
  create: () => (() => void) | void,
  deps: Array<mixed> | void | null,
): void
```

**Usage**

Use this hook when you need to perform DOM measurements (like getting an element's scroll position or dimensions) and then trigger another render before the user sees any visual inconsistencies. Because it is synchronous, it can block painting, so prefer `useEffect` when possible.

**Example: Measuring an Element's Height**

```javascript
import React, { useState, useLayoutEffect, useRef } from 'react';

function Tooltip() {
  const ref = useRef(null);
  const [tooltipHeight, setTooltipHeight] = useState(0);

  useLayoutEffect(() => {
    // Measure the height of the tooltip element after it has been rendered to the DOM.
    if (ref.current) {
      const height = ref.current.offsetHeight;
      setTooltipHeight(height);
      console.log('Measured height:', height);
    }
  }, []); // Runs once after initial render.

  return (
    <div>
      <div ref={ref} style={{ position: 'absolute', top: '-9999px' }}>
        This is a tooltip with some content.
      </div>
      <p>The tooltip's calculated height is: {tooltipHeight}px</p>
    </div>
  );
}
```

---

## `useInsertionEffect`

`useInsertionEffect` is a specialized hook that runs synchronously *before* React makes any changes to the DOM. Its primary use case is for CSS-in-JS libraries to inject styles into the DOM before layout is calculated.

**This hook is intended for library authors. You will likely not need it in application code.**

**Signature**

```javascript
useInsertionEffect(
  create: () => (() => void) | void,
  deps: Array<mixed> | void | null,
): void
```

**Usage**

CSS-in-JS libraries can use this hook to inject style tags with the highest precedence, ensuring they are available before `useLayoutEffect` runs to read layout information.

**Conceptual Example**

```javascript
// Inside a hypothetical CSS-in-JS library
let ruleCache = new Map();

function useCSS(rule) {
  useInsertionEffect(() => {
    if (!ruleCache.has(rule)) {
      const styleElement = document.createElement('style');
      styleElement.textContent = rule;
      document.head.appendChild(styleElement);
      ruleCache.set(rule, styleElement);
    }
  }, [rule]);
}
```

---

### Summary

Choose your Effect Hook based on the timing required for your side effect.

| Hook | Timing | Common Use Cases |
|---|---|---|
| `useEffect` | Asynchronous, after render and paint | Data fetching, subscriptions, timers. The default choice. |
| `useLayoutEffect` | Synchronous, after render but before paint | Measuring DOM elements, animations that need to be calculated before the next paint. |
| `useInsertionEffect` | Synchronous, before DOM mutations | **For library authors:** Injecting critical styles for CSS-in-JS libraries. |

Next, let's explore how to manage references to values and DOM nodes that don't trigger re-renders. Continue to the [Ref Hooks](./hooks-ref.md) section.