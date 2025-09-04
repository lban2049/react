# Caching

React provides a built-in caching mechanism primarily designed to memoize data fetches and computations within a single server render pass. This helps avoid redundant work and ensures data consistency when the same data is accessed by multiple components in a tree. This is particularly useful in the context of React Server Components.

## `React.cache`

The primary API for this functionality is `React.cache`. It's a higher-order function that wraps another function, memoizing its results based on the arguments it's called with.

### Basic Usage

To use it, import `cache` from React and wrap a function, such as a data-fetching function.

```javascript
// utils/data.js
import { cache } from 'react';

export const getUser = cache(async (id) => {
  const res = await fetch(`https://api.example.com/users/${id}`);
  return res.json();
});
```

Now, within your components, you can call `getUser` as you normally would. During a single render pass on the server, if multiple components call `getUser(123)`, the underlying `async` function will only be executed once. Subsequent calls with the same `id` will receive the cached result.

```javascript
// components/UserProfile.js
import { getUser } from '../utils/data';

async function UserProfile({ id }) {
  const user = await getUser(id);
  return <h1>{user.name}</h1>;
}
```

### How Memoization Works

The `cache` function creates a key based on the arguments passed to the wrapped function.

- **Primitives**: For primitive values like strings, numbers, and booleans, it uses value equality.
- **Objects & Functions**: For objects and functions, it uses reference equality. This means two separate calls with different object instances (even if they have the same content) will result in a cache miss.

```javascript
// Cache miss, because a new object is created for each call
const user1 = await fetchUser({ id: 1 });
const user2 = await fetchUser({ id: 1 });

// Cache hit, because the same object reference is used
const params = { id: 1 };
const user3 = await fetchUser(params);
const user4 = await fetchUser(params);
```

This behavior is implemented using a `WeakMap` for object/function arguments and a `Map` for primitive arguments, creating a tree of cache nodes for each unique sequence of arguments.

### Caching Flow Diagram

The following diagram illustrates the logic for a cached function call.

```d2
direction: down

Component: {
  shape: rectangle
}

Cache-Storage: {
  label: "React's Per-Request Cache"
  shape: cylinder
}

Data-Source: {
  label: "Database / API"
  shape: cylinder
}

cachedFunction: {
  label: "cachedFunction(args)"
  shape: diamond
}

Component -> cachedFunction: "1. Call"

cachedFunction -> Cache-Storage: "2. Check for 'args' key"

Cache-Storage -> cachedFunction: "3a. Cache Hit\n(Return cached value)" {
  style.stroke: "#52c41a"
}

cachedFunction -> Data-Source: "3b. Cache Miss\n(Execute function)" {
  style.stroke: "#faad14"
}

Data-Source -> cachedFunction: "4. Return result"

cachedFunction -> Cache-Storage: "5. Store result against 'args' key"

cachedFunction -> Component: "6. Return value"

```

## Environment-Specific Behavior

The behavior of `cache` differs between server and client environments.

- **Server**: In environments like React Server Components, `cache` performs per-request memoization. The cache is created at the beginning of a request and discarded after the render is complete.
- **Client**: On the client, `cache` currently acts as a no-op. It returns the original function without adding any caching behavior. The API is provided for compatibility, allowing components that use `cache` to run on both the server and client without modification. A full client-side caching implementation may be introduced in a future version.

## `cacheSignal`

To handle request cancellations, React provides the `cacheSignal` function. It returns an `AbortSignal` that is linked to the current request's cache scope. You can pass this signal to operations like `fetch` to automatically cancel them if the render is aborted.

```javascript
import { cache, cacheSignal } from 'react';

export const getPost = cache(async (id) => {
  const signal = cacheSignal();
  const res = await fetch(`https://api.example.com/posts/${id}`, { signal });
  return res.json();
});
```

Like `cache`, `cacheSignal` is primarily for server-side use. On the client, it returns `null`.

## Cache Invalidation

While the server cache is automatically cleared between requests, you may need a way to manually invalidate it on the client, for example after a data mutation. The `useCacheRefresh` Hook serves this purpose.

Calling the `refresh` function returned by `useCacheRefresh` will invalidate the cache, causing cached functions to be re-executed.

```javascript
import { useCacheRefresh } from 'react';

function RefreshButton() {
  const refresh = useCacheRefresh();

  function handleClick() {
    // Invalidate the cache and trigger a re-render
    refresh();
  }

  return <button onClick={handleClick}>Refresh Data</button>;
}
```

---

Understanding React's caching is key to building performant server-rendered applications. For a deeper dive into the differences between rendering environments, see the [Server vs. Client Environments](./advanced-server-vs-client.md) guide.