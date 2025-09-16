# Overview

React is a JavaScript library for building user interfaces. At its core, React allows you to construct complex UIs from small, isolated, and reusable pieces of code called "components." This component-based approach enables you to manage state within each component, making your code more predictable and easier to debug.

The `react` package, currently at version **19.1.0**, contains only the essential functionality to define React components and their state. To actually render these components into a browser or a native mobile view, it must be used with a renderer.

- **For the web**, it is paired with `react-dom`.
- **For native environments**, it is used with `react-native`.

This separation makes React a flexible library that isn't tied to a specific platform, allowing you to learn its principles once and apply them in various environments.

### Key Concepts

React's design is guided by several key principles that make it a powerful tool for frontend development:

<x-cards>
  <x-card data-title="Component-Based Architecture" data-icon="lucide:layout-template">
    Build encapsulated components that manage their own state, then compose them to make complex UIs. Component logic is written in JavaScript instead of templates, so you can easily pass rich data through your app and keep state out of the DOM.
  </x-card>
  <x-card data-title="Stateful Logic with Hooks" data-icon="lucide:hook">
    Hooks are functions that let you “hook into” React state and lifecycle features from function components. Hooks like `useState` and `useEffect` allow you to add state and side-effects to your components without writing a class.
  </x-card>
  <x-card data-title="Platform Agnostic" data-icon="lucide:box">
    The core React library is designed to be independent of any specific rendering target. This allows developers to use the same component model and programming paradigm to build for the web, mobile, and even virtual reality.
  </x-card>
  <x-card data-title="Server and Client Environments" data-icon="lucide:server">
    React is no longer limited to the client. With features like Server Components, React can be used to build applications that leverage both server and client environments for optimal performance and user experience.
  </x-card>
</x-cards>

### Development and Production Modes

By default, React operates in **development mode**. This mode provides helpful warnings and error messages to catch common mistakes during development. When deploying your application, it is crucial to switch to **production mode**. The production build is highly optimized for performance, stripping out the development warnings and minifying the code.

For more detailed information, you can always refer to the official documentation at [react.dev](https://react.dev/).

### Next Steps

Now that you have a high-level understanding of React, the best way to get a feel for it is to build something.

<x-card data-title="Getting Started" data-icon="lucide:rocket" data-href="/getting-started" data-cta="Start Building">
  Follow our practical guide to set up a simple React application and see the core concepts in action.
</x-card>