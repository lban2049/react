# Getting Started

This section guides you through the initial steps of setting up a basic React project. You'll learn how to install React and its renderer, then run a minimal example to quickly begin your client-side development journey.

For an overview of React's purpose and its different runtimes, refer to the [Overview](./overview.md) section.

## Installation

React is typically used with a renderer that targets a specific environment. For web development, you'll commonly use the `react` package alongside `react-dom`. The `react` package provides the core functionality for defining React components, while `react-dom` handles rendering these components into the browser's Document Object Model (DOM).

To start a new React project, you'll need Node.js and npm (Node Package Manager) or Yarn installed. Once you have them, you can install the necessary packages:

```bash
npm install react react-dom
# or
yarn add react react-dom
```

## Minimal Example

Once `react` and `react-dom` are installed, you can create your first React component and render it to the DOM. Below is a minimal example demonstrating a simple counter application using React's `useState` hook and `createRoot` for rendering.

```js
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

This code defines a `Counter` functional component that uses the `useState` hook to manage a `count` state. The component displays the current count and a button to increment it. The `createRoot` function from `react-dom/client` initializes a React root, which is then used to render the `Counter` component into an HTML element with the ID `root`.

## Development vs. Production Builds

By default, React operates in development mode. This version includes extra warnings for common mistakes, which are helpful during the development process. When deploying your application, it's crucial to use the production build. The production version includes additional performance optimizations and strips out all development-only error messages, resulting in a smaller bundle size and faster execution.

Your build tool (like Webpack or Vite) will typically configure this automatically based on your environment variables (e.g., `process.env.NODE_ENV`). Ensure your deployment process correctly sets the environment to production to leverage these optimizations.

---

With React installed and a basic example running, you are now ready to dive deeper into React's fundamental building blocks. Proceed to the [Core Concepts](./core-concepts.md) section to understand components, JSX, state, props, and element types.