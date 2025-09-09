# Core APIs

Welcome to the heart of React. The Core APIs are the fundamental building blocks you'll use to create rich, interactive user interfaces. They provide the essential tools for defining components, describing their structure with JSX, and managing data flow. This section serves as an overview of these foundational concepts. Dive into each topic to gain a comprehensive understanding.

<x-cards data-columns="2">
  <x-card data-title="Components & Props" data-icon="lucide:component" data-href="/core-apis/components-and-props">
    Components are reusable, self-contained pieces of UI. Learn how to build them as functions or classes and pass data through them using props.
  </x-card>
  <x-card data-title="JSX" data-icon="lucide:braces" data-href="/core-apis/jsx">
    JSX is a syntax extension that lets you write HTML-like markup inside your JavaScript code, making UI logic more intuitive and readable.
  </x-card>
  <x-card data-title="Creating & Manipulating Elements" data-icon="lucide:plus-square" data-href="/core-apis/creating-elements">
    Go beyond JSX and learn how to work with React elements directly using functions like `createElement` and `cloneElement` for dynamic UI generation.
  </x-card>
  <x-card data-title="Refs" data-icon="lucide:anchor" data-href="/core-apis/refs">
    Access and interact with DOM nodes or React component instances directly. Refs provide an escape hatch for managing focus, animations, or integrating with third-party libraries.
  </x-card>
  <x-card data-title="Context" data-icon="lucide:share-2" data-href="/core-apis/context">
    Avoid "prop drilling" by passing data through the component tree without having to pass props down manually at every level. Ideal for global data like themes or user authentication.
  </x-card>
  <x-card data-title="Children Utilities" data-icon="lucide:blocks" data-href="/core-apis/children-utilities">
    The `props.children` property can be a complex data structure. The `React.Children` utilities provide safe methods to map, count, and iterate over children.
  </x-card>
</x-cards>

## Next Steps

Mastering these core APIs is the first step toward becoming proficient in React. Once you're comfortable with these concepts, you'll be ready to explore a more modern and powerful way to build components. The next logical step is to dive into Hooks.

Continue to [Hooks](./hooks.md) to learn how to use state and other React features without writing a class.