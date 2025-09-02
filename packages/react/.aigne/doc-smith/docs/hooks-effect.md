# Effect Hooks

Effect Hooks allow you to perform side effects in function components. Side effects are operations that can affect other components and can't be done during rendering, such as data fetching, setting up a subscription, or manually changing the DOM.

These hooks provide a way to synchronize a component with an external system. This section covers the three primary effect hooks, each designed for a specific timing in the component lifecycle:

- `useEffect`: For the majority of side effects. It runs *after* render and browser paint.
- `useLayoutEffect`: For effects that need to run synchronously after DOM mutations but *before* the browser paints. This is useful for measuring layout.
- `useInsertionEffect`: A specialized hook for CSS-in-JS libraries to inject styles *before* DOM mutations.

```d2
direction: down

"Start Render": {
  shape: circle
}

"Render Phase": "React renders components"

"Commit Phase": {
  "1. useInsertionEffect": "Fires before DOM mutations"
  "2. DOM Mutations": "React updates the DOM"
  "3. useLayoutEffect": "Fires after DOM mutations"
}

"Browser Paint": "Browser paints the screen"

"useEffect": "Fires after paint"

"End": {
  shape: circle
}

"Start Render" -> "Render Phase"
"Render Phase" -> "Commit Phase"
"Commit Phase" -> "Browser Paint"
"Browser Paint" -> "useEffect"
"useEffect" -> "End"
```

## useEffect

`useEffect` is the most common hook for managing side effects. It runs asynchronously after the render is committed to the screen, ensuring that the effect code doesn't block the browser painting process.

### Syntax

```javascript
useEffect(create, deps);
```

### Parameters

| Parameter | Type | Description |
|---|---|---|
| `create` | `() => (() => void) \| void` | A function that contains the side effect logic. It can optionally return a cleanup function, which React runs before the component unmounts or before the effect is re-run. |
| `deps` | `Array<mixed> \| void \| null` | An array of dependencies. The effect will only re-run if one of these dependencies has changed since the last render. If you pass an empty array (`[]`), the effect runs only once after the initial render. If omitted, the effect runs after every render. |

### Example: Fetching Data

```javascript
import React, { useState, useEffect } from 'react';

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    // Effect to fetch user data
    const fetchUser = async () => {
      const response = await fetch(`https://api.example.com/users/${userId}`);
      const data = await response.json();
      setUser(data);
    };

    fetchUser();

    // Cleanup function
    return () => {
      console.log(`Cleaning up effect for user ${userId}`);
      // Here you could cancel the fetch request if it's still pending
    };
  }, [userId]); // Re-run the effect only if userId changes

  if (!user) {
    return <div>Loading...</div>;
  }

  return <h1>{user.name}</h1>;
}
```
In this example, the component fetches user data when it mounts and whenever the `userId` prop changes. The cleanup function ensures that any stale operations are handled before the component unmounts or the effect re-runs.

## useLayoutEffect

`useLayoutEffect` has the same signature as `useEffect`, but it fires synchronously after all DOM mutations are complete and before the browser paints the result. This is useful for reading layout from the DOM and synchronously re-rendering to prevent a visual "flicker."

### Syntax

```javascript
useLayoutEffect(create, deps);
```

### When to use `useLayoutEffect`

You should only use `useLayoutEffect` when your effect needs to perform DOM measurements and trigger a re-render based on those measurements. For example, calculating the position of a tooltip after the content has rendered. Using it for standard effects like data fetching can hurt performance by blocking visual updates.

### Example: Measuring Element Layout

```javascript
import React, { useState, useLayoutEffect, useRef } from 'react';

function Tooltip({ children, targetRef }) {
  const tooltipRef = useRef(null);
  const [position, setPosition] = useState({ top: 0, left: 0 });

  useLayoutEffect(() => {
    if (targetRef.current && tooltipRef.current) {
      const targetRect = targetRef.current.getBoundingClientRect();
      const tooltipRect = tooltipRef.current.getBoundingClientRect();

      // Position the tooltip above the target element
      setPosition({
        top: targetRect.top - tooltipRect.height - 5,
        left: targetRect.left + (targetRect.width / 2) - (tooltipRect.width / 2),
      });
    }
  }, [targetRef]); // Re-calculate when the target changes

  return (
    <div ref={tooltipRef} style={{ ...position, position: 'fixed' }}>
      {children}
    </div>
  );
}
```
Here, `useLayoutEffect` guarantees that by the time the user sees the tooltip, it's already in its correct, calculated position, avoiding any jarring visual jumps.

## useInsertionEffect

`useInsertionEffect` is a specialized hook for CSS-in-JS library authors. It fires synchronously *before* React makes any changes to the DOM. This allows libraries to inject dynamic styles into the document head and ensure they are available before the browser calculates the layout for the new DOM tree.

Application developers should generally not use this hook. Prefer `useEffect` or `useLayoutEffect` for application-level logic.

### Syntax

```javascript
useInsertionEffect(create, deps);
```

### Example: Basic Style Injection

This is a simplified conceptual example of how a CSS-in-JS library might use this hook.

```javascript
import React, { useInsertionEffect } from 'react';

// A global cache for styles to avoid duplicates
const styleCache = new Map();

function useDynamicStyle(className, cssRule) {
  useInsertionEffect(() => {
    if (!styleCache.has(className)) {
      const styleElement = document.createElement('style');
      styleElement.innerHTML = `.${className} { ${cssRule} }`;
      document.head.appendChild(styleElement);
      styleCache.set(className, styleElement);
    }

    return () => {
      // Cleanup logic would be more complex in a real library
    };
  }, [className, cssRule]);
}

function StyledButton() {
  useDynamicStyle('my-button', 'color: white; background-color: blue;');

  return <button className="my-button">Click Me</button>;
}
```
By running before the DOM is updated, `useInsertionEffect` ensures that the `.my-button` class is defined before the `<button>` element is rendered, preventing a flash of unstyled content.

## Summary

Choosing the right Effect Hook depends on the timing of your side effect. For most cases, `useEffect` is the correct choice. When you need to interact with the DOM layout before the browser paints, `useLayoutEffect` is available. For the specific niche of CSS-in-JS style injection, `useInsertionEffect` provides the necessary timing.

Next, let's explore how to manage references to values and DOM nodes without triggering re-renders in the [Ref Hooks](./hooks-ref.md) section.