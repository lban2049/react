# Getting Started

This guide provides a practical introduction to setting up a simple React application. We'll walk through a basic code example to demonstrate how to define a component and render it to the DOM, getting you up and running quickly.

## Basic Usage Example

React allows you to build user interfaces out of individual pieces called components. Here is a simple example of a `Counter` component that displays a number and a button to increment it.

```javascript A Simple Counter Component icon=logos:javascript
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

1.  **`import`**: We import the necessary functions. `useState` is a React Hook for adding state to a component, and `createRoot` is from `react-dom` which is used to render the component into the web page.
2.  **`Counter` Component**: This is a function component. It uses the `useState` Hook to create a `count` state variable initialized to `0`.
3.  **Rendering Logic**: The component returns JSX, which describes the UI. It displays the current `count` and a button. When the button is clicked, it calls `setCount` to update the state, causing React to re-render the component with the new value.
4.  **Mounting the App**: Finally, `createRoot` tells React where to render the application in your HTML—inside the element with the ID `root`. Then, `root.render()` displays the `Counter` component.

To make this code work, you'll need an HTML file with a `<div>` element that has an `id` of `root`. This is typically used in conjunction with a bundler and a development server, but the core concept remains the same.

## Using the Production Build

By default, React runs in development mode, which provides helpful warnings for common mistakes. When you are ready to deploy your application to users, it's crucial to use the production build.

The production version is optimized for performance and strips out development warnings and error messages. For more details on how to set this up, please refer to the official guide on [Optimizing Performance](https://reactjs.org/docs/optimizing-performance.html#use-the-production-build).

## Next Steps

Now that you've seen a basic example, you can dive deeper into React's capabilities:

*   **[Overview](./overview.md)**: Get a high-level introduction to React's purpose and key features.
*   **[API Reference](./api-reference.md)**: Explore the full React API for detailed information on hooks, components, and functions.
*   **[Official Documentation](https://react.dev/)**: Visit the official React website for comprehensive tutorials, guides, and community resources.