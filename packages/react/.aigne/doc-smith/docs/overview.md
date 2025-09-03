# Overview

React is a JavaScript library for creating user interfaces. It allows you to build encapsulated, reusable components that manage their own state, and then compose them to make complex UIs. It's typically used with a renderer like `react-dom` for the web or `react-native` for native environments.

This document provides a high-level introduction to React's core principles and a guide to how this documentation is structured.

## Core Concepts

To understand React, it's helpful to be familiar with a few key concepts.

### Components and JSX

React applications are built from isolated pieces of UI called components. A component is a JavaScript function that returns a piece of UI. Here is a simple example of a `Counter` component:

```javascript
import { useState } from 'react';

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

The HTML-like syntax inside the function is called JSX. It's a syntax extension for JavaScript that allows you to write UI descriptions directly within your code.

### State and Hooks

Components can manage internal data using 'state'. When a component's state changes, React efficiently updates and re-renders the component to reflect the new information. In the example above, `useState` is a **Hook**. Hooks are functions that let you "hook into" React state and lifecycle features from function components, providing a direct and powerful way to manage component behavior.

### Client and Server Environments

The `react` package contains only the functionality necessary to define components. This core library is then used by renderers to translate your components into a user interface on a specific platform. This separation allows React to run in different environments, including on the client (in a web browser) and on the server.

```d2
direction: down

"Your Application" {
  shape: rectangle
}

"React Ecosystem" {
  shape: package
  grid-columns: 1
  grid-gap: 50

  "React Core (`react` package)": {
    shape: hexagon
    "Defines Components, State, Hooks"
  }

  "Renderers": {
    shape: rectangle
    grid-columns: 2
    
    "react-dom": {
      label: "Web (react-dom)"
      "Renders to the DOM"
    }
    
    "react-native": {
      label: "Native (react-native)"
      "Renders to native UI elements"
    }
  }
}

"User Interface": {
  shape: cloud
}

"Your Application" -> "React Ecosystem"."React Core (`react` package)": "Uses"
"React Ecosystem"."React Core (`react` package)" -> "React Ecosystem".Renderers: "Powers"
"React Ecosystem".Renderers -> "User Interface": "Creates"
```

## How This Documentation Is Structured

This documentation is designed to help you, whether you are building your first component or optimizing a large-scale application. It is organized into several key areas:

<x-cards data-columns="2">
  <x-card data-title="Getting Started" data-icon="lucide:play-circle" data-href="/getting-started">
    Set up your development environment and build your first React component.
  </x-card>
  <x-card data-title="Core APIs" data-icon="lucide:cuboids" data-href="/core-apis">
    Learn the fundamental building blocks, including components, JSX, props, and context.
  </x-card>
  <x-card data-title="Hooks" data-icon="lucide:hook" data-href="/hooks">
    A complete guide to all built-in Hooks for managing state, side effects, and performance.
  </x-card>
  <x-card data-title="Advanced Guides" data-icon="lucide:graduation-cap" data-href="/advanced">
    Explore topics like code-splitting, server environments, caching, and transitions.
  </x-card>
</x-cards>

## Next Steps

If you're ready to start building, the best place to begin is the [Getting Started](./getting-started.md) guide. It will walk you through creating a simple React application from scratch.