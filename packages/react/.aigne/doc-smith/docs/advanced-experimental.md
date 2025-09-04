# Experimental APIs

This section covers experimental and unstable APIs available in React. These features are intended for testing and feedback from the community. They are subject to change without notice and should not be used in production environments.

> **Warning:** Experimental APIs may have bugs, undergo significant changes, or be removed entirely in future releases. Use them at your own risk.

## Taint APIs for Security (Server-Only)

The Taint APIs are a security feature designed to prevent sensitive data from being passed from the server environment to the client. When a value is "tainted," React will throw an error if you attempt to include it in props for a Client Component or in a Server Action closure, preventing accidental data leaks.

These APIs are only available in React's server environments.

### `experimental_taintUniqueValue(message, lifetime, value)`

This function taints a unique primitive value, such as a secret key or a user-specific token. It ensures that this specific value cannot be serialized to the client.

**Parameters**

| Name      | Type                                     | Description                                                                                                                              |
|-----------|------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------|
| `message` | `string`                                 | An optional error message to display if an attempt is made to serialize the tainted value.                                               |
| `lifetime`| `object`                                 | An object whose garbage collection lifecycle is tied to the tainted value. When this object is garbage collected, the taint is removed. |
| `value`   | `string` \| `bigint` \| `ArrayBufferView` | The unique primitive value to taint. It cannot be a general object or function.                                                          |

**Example**

```javascript
// In a server-side data fetching function
import { experimental_taintUniqueValue } from 'react';

async function getUserData(userId) {
  const user = await db.users.find({ id: userId });
  const apiSecret = user.apiSecret; // A sensitive value

  // Taint the secret key. The `user` object is used for the lifetime.
  experimental_taintUniqueValue(
    'Do not leak API secrets to the client.',
    user,
    apiSecret
  );

  return user;
}

// If you later try to pass `apiSecret` to a Client Component, React will throw an error.
```

### `experimental_taintObjectReference(message, object)`

This function taints an entire object or function reference. This is useful for tainting complex objects like database connections or configuration instances that should never leave the server.

**Parameters**

| Name      | Type                 | Description                                                                                             |
|-----------|----------------------|---------------------------------------------------------------------------------------------------------|
| `message` | `string`             | An optional error message to display if an attempt is made to serialize the tainted object reference.   |
| `object`  | `object` \| `function` | The object or function reference to taint. It cannot be a primitive value like a string or number.      |

**Example**

```javascript
// In a server-side module
import { experimental_taintObjectReference } from 'react';

// Assume dbConnection is a live database connection object
const dbConnection = createDatabaseConnection();

// Taint the entire database connection object to prevent it from being passed to the client.
experimental_taintObjectReference('Database connection cannot be sent to the client.', dbConnection);

export function getDB() {
  return dbConnection;
}
```

## `postpone(reason)` (Server-Only)

The `postpone` function allows you to interrupt the current server render with a given reason. Unlike `Suspense`, which waits for a promise to resolve, `postpone` is a deliberate choice to stop rendering a component subtree. React may retry the render later. This can be useful for deferring non-essential parts of the UI that depend on slow or non-critical data.

It is only available in React's server environments.

**Parameters**

| Name     | Type     | Description                                               |
|----------|----------|-----------------------------------------------------------|
| `reason` | `string` | A string explaining why the render was postponed.         |

**Example**

```javascript
import { postpone } from 'react';
import { fetchOptionalWidgetData } from './api';

async function OptionalWidget() {
  const data = await fetchOptionalWidgetData();
  if (!data) {
    // If the data isn't available, don't render this component right now.
    // This won't block the initial page load.
    postpone('Optional widget data is not available at this time.');
  }

  return <div>{data.content}</div>;
}
```

## Other Unstable and Experimental APIs

Several other APIs are available under the `unstable_` or `experimental_` prefix. These are exposed for testing in frameworks and libraries.

| API Name                      | Description                                                                                                                            |
|-------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `experimental_useOptimistic`  | An older alias for `useOptimistic`. It is now in Canary and should be used as `useOptimistic` instead. The prefix will be removed. |
| `unstable_Activity`           | A component to control the visibility of offscreen content, often used for features like keep-alive caches in frameworks.         |
| `unstable_SuspenseList`       | A component to coordinate the appearance of multiple `Suspense` boundaries, preventing a jarring UI as content loads in.         |
| `unstable_ViewTransition`     | An API to help create smooth visual transitions between different UI states or views.                                                  |
| `unstable_getCacheForType`    | A mechanism to access a shared cache for a specific type, primarily used for data caching on the server.                               |
| `unstable_useCacheRefresh`    | A Hook that provides a function to invalidate the React cache, triggering a refresh for cached data.                                   |

---

This concludes the overview of React's experimental APIs. While they offer a glimpse into future capabilities, always prefer stable APIs for production applications. For more details on server and client differences, see the [Server vs. Client Environments](./advanced-server-vs-client.md) guide.