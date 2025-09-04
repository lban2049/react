# Getting Started

React is a JavaScript library for creating user interfaces. This guide provides a step-by-step introduction to setting up a new project and creating your first React component. It's designed to get you running as quickly as possible.

## Your First React Component

Let's start with a simple, self-contained example: a counter. This component will display a number and a button that increments the number when clicked. The code below uses functionality from both the `react` and `react-dom` packages.

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

### How It Works

1.  **`import` Statements**: We import `useState`, a React Hook for managing state, from the `react` package. We also import `createRoot` from `react-dom/client`, which is the library responsible for rendering our components in the web browser.

2.  **`Counter` Component**: This is a function component. It's a JavaScript function that returns a description of the UI. Here, it returns a heading (`<h1>`) and a button (`<button>`).

3.  **JSX**: The HTML-like syntax is called JSX. It allows you to write UI structures declaratively within your JavaScript code.

4.  **State Management**: `const [count, setCount] = useState(0);` is a call to the `useState` Hook. It declares a state variable named `count` with an initial value of `0`, and a function `setCount` to update its value. Clicking the button calls `setCount(count + 1)`, which updates the state and tells React to re-render the component with the new `count` value.

5.  **Rendering to the DOM**: The final two lines find a DOM element with the ID `'root'` and tell React to render our `<Counter />` component inside it.

## Development vs. Production Mode

By default, React will be in development mode. The development version includes extra warnings about common mistakes, which is helpful during development. The production version, however, includes performance optimizations and strips out these warning messages.

When deploying your application, always remember to use the [production build](https://reactjs.org/docs/optimizing-performance.html#use-the-production-build) to ensure the best performance for your users.

## What's Next?

You've successfully created your first React component! This is a great starting point. To continue learning, we recommend exploring the following areas:

<x-cards data-columns="2">
  <x-card data-title="Core APIs" data-icon="lucide:cubes" data-href="/core-apis">
    Learn about the fundamental building blocks of React applications, including components, JSX, context, and refs.
  </x-card>
  <x-card data-title="Hooks" data-icon="lucide:hook" data-href="/hooks">
    Dive into all the built-in Hooks that let you use state, effects, and other React features without writing a class.
  </x-card>
</x-cards>

For comprehensive documentation and a full API reference, visit the official React website at [https://react.dev/](https://react.dev/).