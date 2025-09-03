# Core APIs

Welcome to the heart of React. The Core APIs are the fundamental building blocks you'll use to create any React application. They provide the essential tools for defining UI components, managing data flow, and interacting with the browser DOM. Understanding these core concepts is crucial for building robust applications and before diving into more advanced features like Hooks.

At a high level, you write UI structures using JSX, which are transformed into React Elements. These elements are lightweight descriptions of what you want to see on the screen. React then takes these elements and efficiently updates the DOM to match, often through Components that encapsulate logic and state.

```d2
direction: down

"JSX": {
  shape: document
  label: "JSX (UI Syntax)"
}

"React.createElement()": {
  shape: rectangle
}

"React Element": {
  shape: rectangle
  label: "React Element (Object Description of UI)"
}

"Component": {
  shape: package
  label: "Component (Function or Class)"
  "Props": { shape: parallelogram }
  "Context": { shape: parallelogram }
  "Ref": { shape: parallelogram }
}

"React.Children": {
  shape: rectangle
}

"DOM": {
  shape: cylinder
  label: "Browser DOM"
}

"JSX" -> "React.createElement()": "Transpiled into"
"React.createElement()" -> "React Element": "Creates"
"React Element" -> "Component": "Describes an instance of"
"Component" -> "DOM": "Renders to"
"Component"."Props" -> "Component": "Receives data"
"Component" -> "Component"."Context": "Can access"
"Component" -> "Component"."Ref": "Can access"
"React.Children" -> "Component"."Props": "Manipulates props.children"

```

This section provides detailed guides for each of these fundamental building blocks. Explore them to build a strong foundation in React development.

<x-cards data-columns="2">
  <x-card data-title="Components & Props" data-icon="lucide:component" data-href="/core-apis/components-and-props">
    Learn how to create and compose UI using function and class components, and how to pass data using props.
  </x-card>
  <x-card data-title="JSX" data-icon="lucide:code" data-href="/core-apis/jsx">
    Explore how to use JSX syntax to describe your UI and understand the different runtimes for client and server.
  </x-card>
  <x-card data-title="Creating & Manipulating Elements" data-icon="lucide:box-select" data-href="/core-apis/creating-elements">
    Learn about `createElement`, `cloneElement`, and other utilities for working with React elements directly.
  </x-card>
  <x-card data-title="Refs" data-icon="lucide:anchor" data-href="/core-apis/refs">
    Access and interact with DOM nodes or React components directly using `createRef` and `forwardRef`.
  </x-card>
  <x-card data-title="Context" data-icon="lucide:merge" data-href="/core-apis/context">
    Pass data through the component tree without having to pass props down manually at every level.
  </x-card>
  <x-card data-title="Children Utilities" data-icon="lucide:list-tree" data-href="/core-apis/children-utilities">
    Work with the `props.children` opaque data structure using the `React.Children` API helpers.
  </x-card>
</x-cards>


## Next Steps

Mastering these Core APIs will give you a solid foundation for building scalable React applications. Once you are comfortable with these concepts, you are ready to explore how Hooks can add state and other powerful features to your function components.

Continue to the next section to learn about [Hooks](./hooks.md).