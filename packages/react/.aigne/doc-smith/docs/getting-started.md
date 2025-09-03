# Getting Started

React is a JavaScript library for creating user interfaces. This guide provides a step-by-step introduction to setting up a new project and creating your first React component. It's designed to get you from zero to a working example quickly.

## Creating Your First Component

The fundamental building block in React is the **component**. A component is a self-contained, reusable piece of UI. Here is a complete example of a simple `Counter` component.

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

### How It Works

Let's break down the code above:

1.  **Imports**: We import `useState` from the `react` package and `createRoot` from `react-dom/client`. `react` contains the core functionality for defining components, while `react-dom` is the renderer that interacts with the web browser's DOM.

2.  **Component Definition**: `Counter` is a function component. It's a JavaScript function that returns a description of the UI. This description is written in JSX, a syntax extension that looks similar to HTML.

3.  **State Management**: `const [count, setCount] = useState(0);` is a call to a React **Hook**. `useState` allows a function component to hold its own state. It returns the current state value (`count`) and a function to update it (`setCount`). We initialize the `count` to `0`.

4.  **UI and Event Handling**: The component returns an `h1` element to display the current `count` and a `button`. The button has an `onClick` event handler that, when triggered, calls `setCount` to update the state by one. When the state changes, React automatically re-renders the component to reflect the new UI.

5.  **Rendering to the DOM**: The final lines connect our React component to the browser. `createRoot` establishes a React root on a DOM element (assuming you have `<div id="root"></div>` in your HTML file), and `root.render(<Counter />);` tells React to render our `Counter` component inside it.

## Development vs. Production Mode

By default, React will be in development mode. The development version includes extra warnings about common mistakes. The production version, however, includes performance optimizations and removes these warning messages.

When deploying your application, it is crucial to use the production build to ensure the best performance for your users. You can find more information in the official documentation on how to [use the production build](https://reactjs.org/docs/optimizing-performance.html#use-the-production-build).

## What's Next?

You've successfully built your first React component. To continue your journey and build more complex applications, we recommend exploring the following topics:

<x-cards data-columns="2">
  <x-card data-title="Core APIs" data-icon="lucide:cuboid" data-href="/core-apis">
    Learn about the fundamental building blocks of React, including Components, Props, JSX, and Context.
  </x-card>
  <x-card data-title="Hooks" data-icon="lucide:anchor" data-href="/hooks">
    Discover all the built-in Hooks that let you use state and other React features without writing a class.
  </x-card>
</x-cards>