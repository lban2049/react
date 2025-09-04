# Overview

React is a JavaScript library for creating user interfaces. As of version **19.1.0**, it provides the foundational tools necessary to build encapsulated components that manage their own state, composing them to make complex UIs.

The `react` package itself contains only the functionality necessary to define components, hooks, and context. It is designed to be used with a renderer, such as `react-dom` for web applications or `react-native` for native mobile environments. This separation allows React to be used in various environments beyond the browser.

```d2
direction: down

"Your Application": {
  shape: rectangle
  label: "Your Application Code\n(Components, Hooks, Logic)"
}

"React Ecosystem": {
  shape: package
  grid-columns: 2
  grid-gap: 50

  "react-package": {
    label: "`react`\nCore Logic"
    shape: package
  }

  "react-renderers": {
    label: "Renderers"
    shape: rectangle
    grid-columns: 1

    "react-dom": {
      label: "`react-dom`\n(For Web)"
      shape: package
    }
    "react-native": {
      label: "`react-native`\n(For Native)"
      shape: package
    }
  }
}

"Platform-UI": {
  shape: rectangle
  label: "Platform UI\n(Browser DOM or Native View)"
}

"Your Application" -> "React Ecosystem"."react-package": "Uses"
"React Ecosystem"."react-package" -> "React Ecosystem"."react-renderers": "Coordinates with"
"React Ecosystem"."react-renderers" -> "Platform-UI": "Renders to"
```

**Note on Builds:** By default, React runs in development mode, which includes helpful warnings for common mistakes. For deployment, you should always use the [production build](https://reactjs.org/docs/optimizing-performance.html#use-the-production-build), which is optimized for performance.

## Basic Usage Example

Here is a simple example of a React component that uses the `useState` Hook to manage a counter.

```javascript
import { useState } from 'react';
import { createRoot } from 'react-dom/client';

function Counter() {
  const [count, setCount] = useState(0);
  return (
    <>
      <h1>{count}</h1>
      <button onClick={() => setCount(count + 1)}>
        Increment
      </button>
    </>
  );
}

const root = createRoot(document.getElementById('root'));
root.render(<Counter />);
```

## How This Documentation is Structured

This documentation is designed to help you learn React, whether you are just starting or are an experienced developer. It is organized into several major sections:

<x-cards data-columns="2">
  <x-card data-title="Getting Started" data-icon="lucide:rocket" data-href="/getting-started">
    A step-by-step guide to setting up a new project and creating your first React component.
  </x-card>
  <x-card data-title="Core APIs" data-icon="lucide:cubes" data-href="/core-apis">
    Understand the fundamental building blocks of React, from components and JSX to context and refs.
  </x-card>
  <x-card data-title="Hooks" data-icon="lucide:anchor" data-href="/hooks">
    A detailed guide to all the built-in Hooks that let you use state and other React features without writing a class.
  </x-card>
  <x-card data-title="Advanced Guides" data-icon="lucide:graduation-cap" data-href="/advanced">
    Dive deeper into more complex React features, patterns, and environments like server components and caching.
  </x-card>
</x-cards>

## Next Steps

To start building your first application, proceed to the [Getting Started](./getting-started.md) guide.