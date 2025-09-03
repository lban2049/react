# Advanced Guides

Once you have a solid grasp of React's core concepts, the advanced guides will help you tackle more complex challenges in building modern, high-performance applications. This section explores powerful features for optimizing performance, managing complex state updates, and understanding React's behavior in different environments.

These topics are designed for developers looking to deepen their understanding of React's capabilities and build more sophisticated applications.

<x-cards data-columns="2">
  <x-card data-title="Server vs. Client Environments" data-icon="lucide:server-cog" data-href="/advanced/server-vs-client">
    Understand the key differences between rendering React on the server and the client, and learn about the specific APIs available in each environment.
  </x-card>
  <x-card data-title="Code Splitting with lazy and Suspense" data-icon="lucide:scissors" data-href="/advanced/code-splitting">
    Improve your application's initial load time by splitting your code into smaller chunks and loading components on demand using React.lazy and Suspense.
  </x-card>
  <x-card data-title="Caching" data-icon="lucide:database" data-href="/advanced/caching">
    Explore React's built-in caching capabilities to memoize function results and optimize data fetching, improving both performance and user experience.
  </x-card>
  <x-card data-title="Transitions" data-icon="lucide:fast-forward" data-href="/advanced/transitions">
    Learn how to use transitions to keep your user interface responsive during complex screen updates, preventing blocking renders for a smoother experience.
  </x-card>
  <x-card data-title="Experimental APIs" data-icon="lucide:flask-conical" data-href="/advanced/experimental">
    Get a glimpse into the future of React by exploring experimental APIs. These features are still in development and provide an opportunity for early feedback.
  </x-card>
</x-cards>

## How Advanced Features Work Together

Many of these advanced features are designed to work in concert. For example, a user interaction can trigger a transition that involves lazily loading a new component, which in turn uses a cached function to fetch its data, all orchestrated seamlessly with Suspense.

```d2
direction: down

"User Interaction": {
  shape: circle
}

"startTransition()": {
  shape: rectangle
}

"Suspense Boundary": {
  shape: package
  grid-columns: 1

  "UI shows fallback": {
    shape: rectangle
    style.stroke-dash: 2
  }

  "Lazy Component": {
    shape: rectangle
  }

  "cache() function": {
    shape: rectangle
  }

  "Lazy Component" -> "cache() function": "Fetches data"
}

"User Interaction" -> "startTransition()": "Wraps state update"
"startTransition()" -> "Suspense Boundary": "Triggers render"
"Suspense Boundary"."Lazy Component" -> "UI shows fallback": "Suspends"
```

## Next Steps

These guides provide the tools you need to build sophisticated and highly optimized React applications. After exploring these topics, you may find it useful to consult the complete [API Reference](./api-reference.md) for in-depth details on specific functions and hooks.