# API Reference

This section provides an overview of the public APIs exposed by `react-cache`, an experimental utility designed for data caching in React applications. These APIs allow you to define and manage cacheable resources and control the global cache behavior.

It is important to remember that `react-cache` is in an experimental stage. For detailed information on its pre-release status and usage warnings, refer to the [Experimental Status and Warnings](./Experimental-Status-and-Warnings.md) section.

## Available APIs

### `unstable_createResource`

The `unstable_createResource` function is used to define a cacheable resource. It takes a data fetching function and an optional key hashing function, returning a `Resource` object. This `Resource` object provides `read` and `preload` methods to interact with the cached data.

To learn more about how to create and manage resources, including handling data states and defining custom keys, visit the detailed documentation for [`unstable_createResource`](./API-Reference-unstable_createResource.md).

### `unstable_setGlobalCacheLimit`

The `unstable_setGlobalCacheLimit` function allows you to adjust the maximum number of entries the global `react-cache` LRU (Least Recently Used) cache can hold. This directly influences when older, less frequently accessed entries are evicted from the cache to make room for new data.

For a comprehensive guide on setting and understanding the implications of the global cache limit, refer to the documentation for [`unstable_setGlobalCacheLimit`](./API-Reference-unstable_setGlobalCacheLimit.md).

---

This section introduced the primary APIs of `react-cache`. For a deeper dive into how these APIs are built and their underlying mechanisms, you might find the [Core Concepts](./Core-Concepts.md) documentation helpful. It is highly recommended to review the [Experimental Status and Warnings](./Experimental-Status-and-Warnings.md) before considering any use of this package.