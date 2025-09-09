# Getting Started

Welcome to React! This guide will provide a step-by-step walkthrough to create your first React component. React is a JavaScript library for building user interfaces, and this introduction is designed to get you up and running quickly.

We will build a simple counter application that demonstrates the fundamental concepts of components, state, and rendering.

### Your First React Component

Here is a complete, working example of a simple counter component. We'll break down how it works in the following sections.

```javascript Your First React Component icon=logos:react
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

To make this code work in a browser, you'll need an HTML file to serve as the entry point for your application.

### Step 1: Set Up the HTML File

React components are rendered into a specific DOM element in an HTML file. Create an `index.html` file with a single `<div>` that will act as the root container for our application.

```html index.html icon=logos:html-5
<!DOCTYPE html>
<html>
  <head>
    <title>My First React App</title>
  </head>
  <body>
    <div id="root"></div>
    <!-- Your React code will be loaded here -->
    <script type="module" src="index.js"></script>
  </body>
</html>
```

### Step 2: Define and Render a Component

The core of React is defining reusable components. The `react` package provides the necessary functions to create these components, while a renderer like `react-dom` is used to display them in the browser.

- **`function Counter() { ... }`**: This is a React component. It's a standard JavaScript function that returns a description of the UI, written in a syntax called JSX.
- **`useState(0)`**: This is a **Hook**. It lets us add state to our function component. `useState` returns a pair of values: the current state (`count`) and a function that updates it (`setCount`). We initialize the state to `0`.
- **`onClick={() => setCount(count + 1)}`**: This is an event handler. When the button is clicked, it calls the `setCount` function, passing the new state value (`count + 1`). React then re-renders the `Counter` component with the updated `count`, and the UI changes to display the new number.

### Step 3: Mount the Application

After defining the component, we need to tell React to render it into the root `<div>` we created in our HTML file.

- **`createRoot(document.getElementById('root'))`**: This function from `react-dom/client` creates a React root for the specified DOM container.
- **`root.render(<Counter />)`**: This renders our `Counter` component into the root, making it visible on the screen.

### Development vs. Production Mode

By default, React runs in development mode. This version is helpful during development as it provides extra warnings about common mistakes. When you are ready to deploy your application to users, it's crucial to use the production build. The production version is optimized for performance and strips out development-only warnings.

For more details, see [Use the Production Build](https://reactjs.org/docs/optimizing-performance.html#use-the-production-build).

### Next Steps

You've successfully created your first React component! Now you're ready to dive deeper into the core concepts that make React powerful.

<x-cards>
  <x-card data-title="Core APIs" data-icon="lucide:cuboid" data-href="/core-apis" data-cta="Explore Core APIs">
    Understand the fundamental building blocks of React, from components and JSX to context and refs.
  </x-card>
  <x-card data-title="Hooks" data-icon="lucide:anchor" data-href="/hooks" data-cta="Learn About Hooks">
    A detailed guide to all the built-in Hooks that let you use state and other features without writing a class.
  </x-card>
</x-cards>