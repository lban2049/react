# Advanced Guides

Once you have a solid grasp of the [Core APIs](./core-apis.md) and [Hooks](./hooks.md), you're ready to explore React's more powerful features. This section delves into advanced patterns and APIs for building complex, high-performance applications. Here you'll learn about optimizing your app's performance, handling different rendering environments, and managing complex UI state updates gracefully.

These guides are designed for developers who are comfortable with the fundamentals of React and are looking to tackle more challenging problems.

<x-cards data-columns="2">
  <x-card data-title="Server vs. Client Environments" data-icon="lucide:server-cog" data-href="/advanced/server-vs-client">
    Learn the key differences between React's server and client environments and how to use the appropriate APIs for each to build universal applications.
  </x-card>
  <x-card data-title="Code Splitting with lazy and Suspense" data-icon="lucide:scissors" data-href="/advanced/code-splitting">
    Improve your application's initial load time by splitting your code into smaller chunks and loading components on demand with `React.lazy` and `Suspense`.
  </x-card>
  <x-card data-title="Caching" data-icon="lucide:database" data-href="/advanced/caching">
    Explore React's built-in caching mechanisms, like `cache`, to memoize data requests and computations, reducing redundant work on both the server and the client.
  </x-card>
  <x-card data-title="Transitions" data-icon="lucide:hourglass" data-href="/advanced/transitions">
    Keep your user interface responsive during complex screen updates. Use transitions to mark non-urgent updates, preventing them from blocking user input.
  </x-card>
  <x-card data-title="Experimental APIs" data-icon="lucide:flask-conical" data-href="/advanced/experimental">
    Get a first look at upcoming features. These APIs are not yet stable but offer a glimpse into the future of React and an opportunity to provide feedback.
  </x-card>
</x-cards>

Each guide provides in-depth explanations and practical examples to help you master these concepts. Choose a topic that interests you to get started, or explore the complete [API Reference](./api-reference.md) for detailed documentation on every function and component.