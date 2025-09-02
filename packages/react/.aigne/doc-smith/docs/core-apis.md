# Core APIs

Welcome to the core of React. These are the fundamental building blocks you will use to create rich, interactive user interfaces. Understanding these APIs is essential for building any React application, from a simple widget to a large-scale enterprise platform. This section provides a high-level overview and directs you to detailed guides for each concept.

At a high level, you write UI descriptions in JSX, which are transformed into React Elements. These elements are the output of your Components, which receive data via props. For more complex data flow, Context allows you to pass data through the component tree, while Refs give you a way to interact directly with DOM nodes or component instances.

```d2
direction: down

"JSX" -> "React.createElement()": "Compiles to" {
  style.animated: true
}

"React.createElement()" -> "React Element": "Creates" {
  style.animated: true
}

"Component": {
  shape: hexagon
  "Receives Props"
  "Returns React Element"
}

"Component" -> "React Element"

"App Tree": {
  "Context Provider"

  "Component A": {
    "Ref" -> "DOM Node": Accesses {
      style.stroke-dash: 4
    }
  }

  "Component B"
}

"Context Provider" -> "Component B": "Provides data" {
    style.stroke-dash: 4
}
```

Dive into the specifics of each core concept through the guides below.

<x-cards data-columns="2">
  <x-card data-title="Components & Props" data-icon="lucide:layout-template" data-href="/core-apis/components-and-props">
    The heart of every React application. Learn how to build reusable UI pieces with function and class components and pass data through them using props.
  </x-card>
  <x-card data-title="JSX" data-icon="lucide:code" data-href="/core-apis/jsx">
    Explore the syntax extension for JavaScript that allows you to write UI descriptions that look like HTML. Understand how it compiles to regular JavaScript function calls.
  </x-card>
  <x-card data-title="Creating & Manipulating Elements" data-icon="lucide:box-select" data-href="/core-apis/creating-elements">
    Go behind the scenes of JSX to learn about `createElement` and `cloneElement`. These utilities are foundational for creating and manipulating React elements directly.
  </x-card>
  <x-card data-title="Refs" data-icon="lucide:anchor" data-href="/core-apis/refs">
    Learn how to create and use refs to get direct access to a DOM element or a React component instance, essential for managing focus, media playback, or integrating with third-party libraries.
  </x-card>
  <x-card data-title="Context" data-icon="lucide:share-2" data-href="/core-apis/context">
    Manage global state or avoid "prop drilling" by passing data through the component tree without having to pass props down manually at every level.
  </x-card>
  <x-card data-title="Children Utilities" data-icon="lucide:blocks" data-href="/core-apis/children-utilities">
    Discover the `React.Children` utilities, which provide helpful methods like `map`, `forEach`, and `toArray` for working with the opaque `props.children` data structure.
  </x-card>
</x-cards>

### Next Steps

After you are comfortable with these core APIs, you'll be ready to explore the powerful world of Hooks, which let you use state, lifecycle features, and other capabilities within function components.

➡️ **Continue to: [Hooks](./hooks.md)**