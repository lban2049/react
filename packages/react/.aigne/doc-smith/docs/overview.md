# Overview

Welcome to the documentation for `react`, a JavaScript library for creating user interfaces. Whether you're building a simple web page or a complex single-page application, React provides the tools to build modern, fast, and scalable UIs with a component-based architecture.

This core `react` package contains the essential functionality for defining React components and managing their state. It is almost always used in combination with a renderer, such as `react-dom` for web applications or `react-native` for mobile environments.

## Core Principles

React is built around a few key ideas:

*   **Component-Based Architecture:** Build encapsulated components that manage their own state, then compose them to make complex UIs. This makes your code more reusable and easier to reason about.
*   **Declarative Syntax:** Describe *what* your UI should look like for any given state, and React will efficiently update and render just the right components when your data changes.
*   **Versatile Environments:** While it's most famous for client-side web development, React is also designed to render on the server, offering powerful solutions for performance and SEO.

## How to Use These Docs

This documentation is structured to help you find what you need quickly, whether you're a beginner or an experienced developer.

<x-cards data-columns="2">
  <x-card data-title="Getting Started" data-icon="lucide:rocket" data-href="/getting-started">
    New to React? This is the place to start. We'll guide you through setting up your first React application.
  </x-card>
  <x-card data-title="Core APIs" data-icon="lucide:cubes" data-href="/core-apis">
    Dive into the fundamental building blocks of React, including components, JSX, context, and refs.
  </x-card>
  <x-card data-title="Hooks" data-icon="lucide:anchor" data-href="/hooks">
    Learn about Hooks, the functions that let you “hook into” React state and lifecycle features from function components.
  </x-card>
  <x-card data-title="Advanced Guides" data-icon="lucide:graduation-cap" data-href="/advanced">
    Explore more complex topics like server environments, code splitting, performance optimization, and experimental features.
  </x-card>
</x-cards>

## A First Look

Here is a simple example of a React component. It's a self-contained counter that uses the `useState` Hook to manage its state.

```javascript A Simple Counter Component icon=logos:react
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

## Development and Production Modes

By default, React runs in development mode, which includes helpful warnings and debugging tools. When you're ready to deploy your application to users, it's crucial to switch to production mode.

The production version is highly optimized for performance and strips out all development-only warnings. Don't forget to use the [production build](https://reactjs.org/docs/optimizing-performance.html#use-the-production-build) when deploying your application.

---

Ready to get your hands dirty? Let's move on to the [Getting Started](./getting-started.md) guide to set up your development environment.