# Getting Started

This section guides you on how to begin using `react-cache` for demonstration and experimentation. As an experimental package, `react-cache` is primarily intended for exploring potential future React features and serves as a reference for advanced caching implementations. For a high-level introduction to the package, refer to the [Overview](./Overview.md) section.

**It is critical to understand that `react-cache` is unstable and not suitable for use in real applications. The API is subject to frequent and significant changes.**

## Installation

`react-cache` is published as an alpha version and can be added to your project for experimentation. Ensure you have `react` version 17.0.0 or higher installed, as it is a peer dependency.

You can install it using npm or yarn:

```bash
npm install react-cache@2.0.0-alpha.0
# or
yarn add react-cache@2.0.0-alpha.0
```

After installation, you can import its functionalities into your React application.

## Basic Usage and Warnings

`react-cache` exposes `unstable_createResource` for creating cacheable data resources and `unstable_setGlobalCacheLimit` for adjusting the cache size. When working with `react-cache`, always remember its experimental nature.

Below is a conceptual example of how you might create and use a resource. Note that `read` and `preload` methods can only be called from within a component's render phase.

```javascript
import { unstable_createResource, unstable_setGlobalCacheLimit } from 'react-cache';

// Define a function that fetches data (e.g., from an API)
const fetchData = async (id) => {
  // Simulate an async operation
  return new Promise(resolve => {
    setTimeout(() => {
      console.log(`Fetching data for ID: ${id}`);
      resolve(`Data for ${id}`);
    }, 1000);
  });
};

// Create a cacheable resource using unstable_createResource
// The first argument is the fetcher function.
// The second optional argument is a hash function for complex keys.
const myResource = unstable_createResource(fetchData);

// You can adjust the global cache limit. Default is 500.
unstable_setGlobalCacheLimit(1000);

function MyComponent({ itemId }) {
  // In a real scenario, you would call read() within the render method
  // For demonstration, this is how you might access the cached data or trigger a fetch.
  try {
    const data = myResource.read(itemId);
    return <div>Displaying: {data}</div>;
  } catch (suspender) {
    // If data is not yet resolved, read() will throw a suspender
    // React's Suspense component will catch this.
    return <div>Loading data for {itemId}...</div>;
  }
}

// To preload data (e.g., on a hover or early in the component lifecycle)
// myResource.preload('anotherId');

// Important: Do not use react-cache in production. This is for demonstration and learning purposes only.
```

This example illustrates the primary entry point `unstable_createResource` for defining a cacheable data source. The `read` method is designed to be called within React's render phase, enabling Suspense-driven data loading.

## Next Steps

With `react-cache` set up for experimentation, you can now explore its internal mechanisms and API in more detail. Proceed to the [Core Concepts](./Core-Concepts.md) section to understand its foundational principles, or directly to the [API Reference](./API-Reference.md) for detailed documentation of `unstable_createResource` and `unstable_setGlobalCacheLimit`.