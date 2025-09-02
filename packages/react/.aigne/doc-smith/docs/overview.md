# Overview

React is a JavaScript library for creating user interfaces. Its primary purpose is to allow developers to build encapsulated components that manage their own state, which can then be composed to create complex and interactive UIs. The core package, `react`, contains only the functionality necessary to define components and is typically used with a renderer like `react-dom` for web applications or `react-native` for native environments.

## Core Principles

React is designed around several key ideas that make UI development predictable and efficient.

<x-cards data-columns="3">
  <x-card data-title="Declarative" data-icon="lucide:glasses">
    Describe what your UI should look like for any given state, and React will manage the updates when the data changes.
  </x-card>
  <x-card data-title="Component-Based" data-icon="lucide:blocks">
    Build UIs from small, isolated pieces of code called “components.” This makes your code more reusable and easier to reason about.
  </x-card>
  <x-card data-title="Platform Agnostic" data-icon="lucide:globe">
    The core React logic can be used with different renderers, allowing you to share code between the web, mobile, and other environments.
  </x-card>
</x-cards>

### The React Ecosystem

The `react` package provides the core APIs for defining components, while renderers handle the platform-specific logic of displaying them. This separation allows React to be adapted to new platforms.

```d2
direction: down

"react": {
  label: "React Core\n(Defines components & state)"
  shape: package
}

renderers: {
  label: "Renderers"
  "react-dom": {
    label: "React DOM\n(Renders to the browser)"
    shape: package
  }
  "react-native": {
    label: "React Native\n(Renders to native platforms)"
    shape: package
  }
}

platforms: {
  label: "Platforms"
  "Web Application (Browser)"
  "Mobile Application (iOS/Android)"
}

"react" -> renderers: "Used by"
renderers."react-dom" -> platforms."Web Application (Browser)": Renders
renderers."react-native" -> platforms."Mobile Application (iOS/Android)": Renders

```

**Note on Environment Modes**

By default, React runs in development mode, which includes extra warnings about common mistakes. When deploying your application, it's important to use the [production build](https://reactjs.org/docs/optimizing-performance.html#use-the-production-build), which includes performance optimizations and removes development-only messages.

## How This Documentation is Structured

This documentation is organized to help you find information efficiently, whether you're just starting or exploring advanced topics. 

<x-cards data-columns="2">
  <x-card data-title="Getting Started" data-icon="lucide:rocket" data-href="/getting-started">
    A step-by-step guide to setting up a new project and creating your first React component.
  </x-card>
  <x-card data-title="Core APIs" data-icon="lucide:puzzle" data-href="/core-apis">
    Understand the fundamental building blocks of React applications, from components and JSX to context and refs.
  </x-card>
  <x-card data-title="Hooks" data-icon="lucide:anchor" data-href="/hooks">
    A detailed guide to all the built-in Hooks that let you use state and other React features without writing a class.
  </x-card>
  <x-card data-title="Advanced Guides" data-icon="lucide:graduation-cap" data-href="/advanced">
    Dive deeper into more complex React features, patterns, and environments.
  </x-card>
  <x-card data-title="API Reference" data-icon="lucide:book-open" data-href="/api-reference">
    A comprehensive, A-Z reference for all public React APIs, including components, hooks, and utilities.
  </x-card>
  <x-card data-title="Package Exports" data-icon="lucide:package" data-href="/package-exports">
    Details on the different entry points provided by the 'react' package, such as `jsx-runtime` and `compiler-runtime`.
  </x-card>
</x-cards>


## Next Steps

Ready to start building? The best place to begin is the [Getting Started](./getting-started.md) guide, which will walk you through creating your first React application.