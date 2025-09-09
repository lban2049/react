# Caching

React provides a powerful, built-in caching mechanism designed for memoizing data fetches and computations within a single server-rendering pass. This allows you to fetch data from multiple components without making redundant requests for the same information, streamlining your server-side logic and improving performance.

It's crucial to understand that React's caching capabilities behave differently depending on the environment. On the server, it provides a per-request cache. On the client, the `cache` function currently has no caching behavior and acts as an identity function.

## The `cache` Function

The primary API for caching in React is the `cache` function. You wrap a function (e.g., a data-fetching function) with `cache` to memoize its results.

When you call a cached function with a set of arguments, React stores the result. If you call the same function with the same arguments later in the same render pass, React will return the stored result without re-executing the function.

### Server-Side Usage

On the server, `cache` is highly effective for deduplicating data requests. The cache is created for each server request and discarded after the request is complete.

```javascript Server-Side Data Fetching icon=logos:react
import { cache } from 'react';

export const getUser = cache(async (userId) => {
  const response = await fetch(`https://api.example.com/users/${userId}`);
  if (!response.ok) {
    throw new Error('Failed to fetch user');
  }
  return response.json();
});

// --- In ComponentA.js ---
// const user = await getUser(123); // Makes a network request

// --- In ComponentB.js (during the same render) ---
// const user = await getUser(123); // Returns cached data, no new network request
```

### Client-Side Behavior

In client-side environments (i.e., not in a React Server Components environment), `cache` does not perform any caching. It simply returns the original function. This API is exposed on the client so that shared components that use `cache` on the server can run on the client without breaking. Developers must be aware of this behavioral difference.

```javascript Client-Side Behavior icon=logos:react
// In a client-side environment, this code...
import { cache } from 'react';

const calculate = (a, b) => a + b;
const cachedCalculate = cache(calculate);

// ...behaves identically to this:

const calculate = (a, b) => a + b;
const cachedCalculate = calculate;
```

### How Caching Works

The caching mechanism generates a key based on the arguments passed to the function.

- **Primitive arguments** (string, number, boolean, etc.) are stored in a `Map`.
- **Object and function arguments** are stored in a `WeakMap`. Using a `WeakMap` ensures that the cache doesn't prevent garbage collection of objects that are no longer referenced elsewhere, preventing memory leaks.

If a cached function throws an error, React caches the error. Subsequent calls with the same arguments will re-throw the same cached error without re-executing the function.

## The `cacheSignal` Function

When performing data fetching, it's good practice to handle request cancellation. The `cacheSignal` function provides an `AbortSignal` that is tied to the lifecycle of the server request.

This signal will be aborted if the render it's part of is cancelled. You can pass this signal to `fetch` to automatically cancel the network request.

```javascript Using cacheSignal with fetch icon=logos:react
import { cache, cacheSignal } from 'react';

const fetchPost = cache(async (postId) => {
  const signal = cacheSignal();
  const response = await fetch(`https://api.example.com/posts/${postId}`, {
    signal, // Pass the signal to fetch
  });
  return response.json();
});
```

Like `cache`, `cacheSignal` is primarily for server-side use. On the client, it returns `null`.

---

By understanding React's caching APIs, you can build more efficient server-rendered applications. The key is to remember its per-request nature on the server and its pass-through behavior on the client. For a deeper dive into the environmental differences, see our guide on [Server vs. Client Environments](./advanced-server-vs-client.md).