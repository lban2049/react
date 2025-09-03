# Caching

React provides built-in caching capabilities primarily designed for data fetching and memoization within Server Components. These tools help prevent redundant data requests during a single render pass, improving performance and ensuring data consistency across your component tree. This guide covers the `cache` function and related APIs for managing the request lifecycle.

For a broader understanding of the different rendering environments, see the [Server vs. Client Environments](./advanced-server-vs-client.md) guide.

## `cache` Function

The `cache` function is the primary API for memoizing the result of a function. When you wrap a function with `cache`, React stores the return value for a given set of arguments. If the same function is called with the same arguments later in the same server render pass, React will return the stored value instead of re-executing the function.

This is particularly useful for data fetching functions that are called from multiple components in a tree.

### Server-Side Usage

On the server, `cache` memoizes function calls on a per-request basis. It handles both successful results and thrown errors, ensuring that a failed data fetch doesn't re-execute and throw the same error multiple times within a single render.

```javascript
import { cache } from 'react';

export const getUser = cache(async (id) => {
  const res = await fetch(`https://api.example.com/users/${id}`);
  if (!res.ok) {
    throw new Error('Failed to fetch user');
  }
  return res.json();
});

// Component 1
async function UserProfile({ id }) {
  const user = await getUser(id); // Network request is made
  return <h1>{user.name}</h1>;
}

// Component 2 (in the same tree)
async function UserHeader({ id }) {
  const user = await getUser(id); // Cached result is returned instantly
  return <header>Welcome, {user.name}</header>;
}
```

In this example, even though `UserProfile` and `UserHeader` both call `getUser(id)`, the actual `fetch` request will only be executed once. The second call will receive the cached data.

### Client-Side Behavior

By default, the `cache` function has no caching behavior on the client. It acts as a no-op, meaning it returns the original function, which will be executed on every call. This design allows you to write shared components that use `cache` on the server without breaking on the client. However, you must be aware that the function will not be memoized in a client environment.

### Caching Mechanism

The `cache` function builds a nested map structure to store results. It uses a `WeakMap` for object and function arguments and a standard `Map` for primitive arguments (string, number, boolean, etc.). This ensures that objects can be garbage-collected if they are no longer referenced elsewhere, preventing memory leaks.

Here is a simplified visualization of the caching structure:

```d2
direction: down

call: "cachedFn(1, { id: 'a' })" {
  label: "Function Call"
  shape: oval
}

tree: "Cache Tree" {
  shape: package
  grid-columns: 1

  fn_ref: "Function Reference (WeakMap)" {
    shape: package

    fn_node: "fn -> Node 1" {
      shape: rectangle
      
      primitive_args: "Primitive Args (Map)" {
        shape: package

        primitive_node: "1 -> Node 2" {
          shape: rectangle
          
          object_args: "Object Args (WeakMap)" {
            shape: package

            result_node: "{id: 'a'} -> Node 3 (Result)" {
              shape: document
              "status: TERMINATED"
              "value: { ... }"
            }
          }
        }
      }
    }
  }
}

call -> tree.fn_ref: "Traverses tree to find or store result"

```

## Related APIs

Several other APIs work in conjunction with React's caching system.

<x-cards data-columns="2">
  <x-card data-title="cacheSignal" data-icon="lucide:signal">
    A function that returns an `AbortSignal` tied to the current request. You can pass this signal to fetch requests to automatically cancel them if the render is aborted. On the client, it returns `null`.
  </x-card>
  <x-card data-title="unstable_useCacheRefresh" data-icon="lucide:refresh-cw">
    A Hook that returns a function to invalidate the entire cache and trigger an update. This is useful for implementing features like a "refresh" button in a client component to refetch server data.
  </x-card>
</x-cards>

### Example: Using `cacheSignal` and `useCacheRefresh`

Here’s how you can combine these APIs for robust data fetching.

**Server Data Fetching Function:**

```javascript
// lib/data.js
import { cache, cacheSignal } from 'react';

export const getItems = cache(async () => {
  const signal = cacheSignal();
  const res = await fetch('https://api.example.com/items', { signal });
  return res.json();
});
```

**Client Component with Refresh Button:**

```javascript
'use client';

import { useTransition, unstable_useCacheRefresh as useCacheRefresh } from 'react';

export function RefreshButton() {
  const refresh = useCacheRefresh();
  const [isPending, startTransition] = useTransition();

  const handleRefresh = () => {
    startTransition(() => {
      refresh();
    });
  };

  return (
    <button onClick={handleRefresh} disabled={isPending}>
      {isPending ? 'Refreshing...' : 'Refresh Data'}
    </button>
  );
}
```

## Environment Behavior Summary

The behavior of caching APIs differs significantly between server and client environments. The following table summarizes these differences:

| API                          | Server Environment                               | Client Environment (Default)                                   |
| ---------------------------- | ------------------------------------------------ | -------------------------------------------------------------- |
| `cache(fn)`                  | Memoizes `fn` for the duration of a request.     | No-op. Returns `fn` without any caching behavior.              |
| `cacheSignal()`              | Returns an `AbortSignal` for the current request. | Returns `null`.                                                |
| `unstable_useCacheRefresh()` | Not applicable (it's a Hook).                    | Returns a function to invalidate the cache and trigger a re-render. |

Understanding these distinctions is key to effectively using React's caching features in applications that span both server and client execution.

---

With a grasp of React's caching mechanisms, you can build more performant server-rendered applications. To explore another advanced feature for non-blocking UI updates, proceed to the [Transitions](./advanced-transitions.md) guide.