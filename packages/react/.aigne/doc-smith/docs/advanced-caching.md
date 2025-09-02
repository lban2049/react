# Caching

React provides built-in caching capabilities to memoize the result of data fetching or computations. This is particularly powerful in server environments, such as with React Server Components, where you can fetch data in multiple components without causing redundant requests for the same data within a single render pass.

This guide covers the `cache` function for memoization, how its behavior differs between server and client, and related APIs for managing cached operations.

## The `cache` Function

The primary API for caching is the `cache` function. It wraps a function, memoizing its return value. When you call the wrapped function with the same arguments multiple times within the same server render, it will only execute the original function once. Subsequent calls will receive the cached result.

This is especially useful for data-fetching functions that might be called in various components throughout the component tree.

**Example Usage**

```javascript
import { cache } from 'react';

// Define a data fetching function
const fetchUserDetails = async (userId) => {
  const response = await fetch(`https://api.example.com/users/${userId}`);
  if (!response.ok) {
    throw new Error('Failed to fetch user');
  }
  return response.json();
};

// Wrap it with cache
export const getUser = cache(fetchUserDetails);
```

Now, any Server Component can call `getUser(id)` without worrying about duplicate network requests for the same user during a single request-response lifecycle.

```jsx
// ComponentA.js
import { getUser } from './data';

async function ComponentA({ id }) {
  const user = await getUser(id); // Fetches and caches the user
  return <div>{user.name}</div>;
}

// ComponentB.js
import { getUser } from './data';

async function ComponentB({ id }) {
  const user = await getUser(id); // Returns the cached user data
  return <p>Email: {user.email}</p>;
}
```

### How It Works

The `cache` function works by creating a nested map structure based on the arguments passed to the wrapped function. The cache is scoped to each server request, ensuring that data from one user's request doesn't leak into another's.

- **Primitive Arguments**: Values like strings, numbers, and booleans are keyed by their value in a `Map`.
- **Object Arguments**: Objects, functions, and symbols are keyed by their reference in a `WeakMap`.

If the wrapped function throws an error, the error is also cached. Any subsequent call with the same arguments will re-throw the cached error.

The underlying mechanism relies on a request-specific dispatcher to store the cache data.

```d2
direction: down

"Request Arrives": {
  shape: step
  "React Render Pass": {
    "getUser(123)": {
      "cache(fetchUserDetails)": {
        label: "Is there a cached result for fn + args?"
        shape: diamond
      }
    }
  }
}

"Request Arrives" -> "React Render Pass.getUser(123).cache(fetchUserDetails)"

"React Render Pass.getUser(123).cache(fetchUserDetails)" -> "Execute fetchUserDetails(123)": {
  label: No
  style.stroke: red
}

"Execute fetchUserDetails(123)" -> "Store result in request cache": {
  shape: cylinder
}

"Store result in request cache" -> "Return result"

"React Render Pass.getUser(123).cache(fetchUserDetails)" -> "Return cached result": {
  label: Yes
  style.stroke: green
}

"Return cached result" -> "Return result": {
  shape: step
}
```

### Environment Differences: Server vs. Client

The behavior of `cache` is fundamentally different depending on the environment. It is primarily designed for the server.

| Environment | `cache` Behavior |
|---|---|
| **Server** | Memoizes function calls for the duration of a single server request. This is the full, intended implementation. |
| **Client** | Acts as a no-op. The wrapped function is executed on every call, and no caching occurs. This is a temporary behavior, and client-side caching may be implemented in a future version. |

This distinction is important for shared components that run in both environments. They must be written to handle the fact that caching is not available on the client.

## Aborting Requests with `cacheSignal`

For asynchronous operations like `fetch`, it's good practice to handle request cancellation. The `cacheSignal` function provides a request-scoped `AbortSignal` that will be aborted if the server-side render is canceled.

```javascript
import { cache, cacheSignal } from 'react';

export const getUser = cache(async (id) => {
  const signal = cacheSignal();
  const response = await fetch(`https://api.example.com/users/${id}`, {
    signal, // Pass the signal to fetch
  });
  return response.json();
});
```

Like `cache`, `cacheSignal` is also environment-dependent:
- **On the server**, it returns an `AbortSignal` tied to the request.
- **On the client**, it returns `null`.

## Unstable: Refreshing the Cache

React exposes an unstable Hook, `useCacheRefresh`, to programmatically invalidate the entire cache for the current request and trigger a re-render.

> **Note:** This API is unstable and should be used with caution. Its behavior and signature may change in future releases.

```jsx
import { unstable_useCacheRefresh as useCacheRefresh } from 'react';

function UserProfile({ id }) {
  const refresh = useCacheRefresh();
  const user = getUser(id);

  return (
    <div>
      <h1>{user.name}</h1>
      <button onClick={() => refresh()}>Refresh</button>
    </div>
  );
}
```

Calling `refresh()` will clear the memoized results from `cache` and re-run the Server Components.

---

Caching is a powerful pattern for optimizing server-rendered applications. To better understand the context where it's most effective, see our guide on [Server vs. Client Environments](./advanced-server-vs-client.md).