# Core APIs

At the heart of any React application are its Core APIs. These are the fundamental building blocks you will use to define, compose, and manage your user interfaces. Understanding these concepts is the key to building robust and maintainable applications. This section provides an overview of these foundational pieces, from creating components with JSX to managing data flow with Context and directly interacting with the DOM using Refs.

These APIs work together to translate your component logic into an efficient representation of the UI, which React then renders to the browser.

```d2
direction: down

"JSX": {
  label: "JSX Syntax\n(e.g., <div />)"
  shape: rectangle
}

"createElement": {
  label: "React.createElement()"
  shape: rectangle
}

"React-Element": {
  label: "React Element\n(Virtual DOM Node)"
  shape: rectangle
}

"Component": {
  label: "Component\n(Class or Function)"
  shape: class
  
  "Props": { shape: parallelogram }
  "State": { shape: parallelogram }
}

"DOM": {
  label: "Browser DOM"
  shape: cylinder
}

"Context": {
  label: "Context"
  shape: package
}

"Ref": {
  label: "Ref"
  shape: rectangle
}

"JSX" -> "createElement": "Compiles to"
"createElement" -> "React-Element": "Creates"
"Component" -> "React-Element": "Renders"
"React-Element" -> "DOM": "Reconciles to"

"Context" -> "Component": "Provides data"
"Ref" -> "DOM": "Accesses"

```

Explore the different building blocks of React's Core API to understand how to structure your application effectively.

<x-cards data-columns="2">
  <x-card data-title="Components & Props" data-href="/core-apis/components-and-props" data-icon="lucide:component">
    Components are the reusable, independent building blocks of a React UI. Learn how to create both class and function components, and how to pass data down the tree using props.
  </x-card>
  <x-card data-title="JSX" data-href="/core-apis/jsx" data-icon="lucide:code-2">
    Explore JSX, a syntax extension for JavaScript that makes describing UI structures intuitive and readable. Understand how it compiles to standard `React.createElement` calls.
  </x-card>
  <x-card data-title="Creating & Manipulating Elements" data-href="/core-apis/creating-elements" data-icon="lucide:plus-square">
    Dive deeper into the `React.createElement` and `cloneElement` functions. These APIs provide direct control for creating and modifying React elements without using JSX.
  </x-card>
  <x-card data-title="Refs" data-href="/core-apis/refs" data-icon="lucide:pin">
    Learn how to access and interact with DOM nodes or React component instances directly using refs. This section covers `createRef` for class components and `forwardRef` for exposing refs through components.
  </x-card>
  <x-card data-title="Context" data-href="/core-apis/context" data-icon="lucide:share-2">
    Manage global state or avoid "prop drilling" by passing data through the component tree using Context. Understand how to use `createContext` to share data with any descendant component.
  </x-card>
  <x-card data-title="Children Utilities" data-href="/core-apis/children-utilities" data-icon="lucide:list-tree">
    Work with the opaque `props.children` data structure effectively. The `React.Children` utilities provide safe methods like `map`, `forEach`, and `toArray` to manipulate children.
  </x-card>
</x-cards>

### Next Steps

Mastering these Core APIs provides the solid foundation needed to build anything with React. Once you are comfortable with these concepts, you'll be ready to explore how Hooks can simplify state management and side effects in function components.

[Next: Hooks](./hooks.md)
