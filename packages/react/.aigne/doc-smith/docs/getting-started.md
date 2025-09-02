# Getting Started

This guide will walk you through creating your first React component. It's designed to give you a practical, hands-on introduction to the core concepts of React.

React is a JavaScript library for creating user interfaces. The `react` package provides the necessary functions to define components, while a renderer like `react-dom` is used to render them to the web.

## Your First React Component

Let's create a simple counter component. This example demonstrates two fundamental React concepts: components and state.

Here is the complete code for a basic interactive counter:

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

### Breaking Down the Code

Let's go through the example step by step to understand how it works.

#### 1. Importing from React and ReactDOM

```javascript
import { useState } from 'react';
import { createRoot } from 'react-dom/client';
```

- `useState`: This is a **Hook**. Hooks let you use state and other React features in function components. `useState` specifically allows you to add state to your components.
- `createRoot`: This function from the `react-dom/client` package sets up the root of your React application, specifying where the UI should be rendered in the DOM.

#### 2. Defining a Component

```javascript
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
```

- `function Counter() { ... }`: This is a React component. It's a JavaScript function that returns a description of the UI, written in a syntax called JSX which resembles HTML.
- `const [count, setCount] = useState(0);`: Here we call the `useState` Hook to add state to our component. `count` is the state variable, which holds the current count (initialized to `0`). `setCount` is a function that updates this state.
- `return (...)`: The component returns the JSX that defines its output. It displays the current `count` in an `<h1>` tag and a button. When the button is clicked (`onClick`), it calls the `setCount` function to increment the `count` state variable.

#### 3. Rendering the Component

```javascript
const root = createRoot(document.getElementById('root'));
root.render(<Counter />);
```

- `createRoot(document.getElementById('root'))`: This tells React to take control of the DOM element with the ID `root`.
- `root.render(<Counter />)`: This renders the `Counter` component into the DOM element managed by `root`.

## Important: Development vs. Production Mode

By default, React runs in development mode. This mode includes helpful warnings for common mistakes. When deploying your application, it's crucial to use the production build, which is optimized for performance and strips out these warnings.

For more details, see [Use the Production Build](https://reactjs.org/docs/optimizing-performance.html#use-the-production-build).

## Next Steps

You've now built your first React component! To continue your journey, we recommend exploring the core building blocks of React applications.

<x-cards>
  <x-card data-title="Core APIs" data-icon="lucide:cuboid" data-href="/core-apis">
    Understand the fundamental concepts like components, JSX, context, and refs.
  </x-card>
  <x-card data-title="Hooks" data-icon="lucide:anchor" data-href="/hooks">
    Dive deeper into all the built-in Hooks that let you add state and other features to your components.
  </x-card>
</x-cards>