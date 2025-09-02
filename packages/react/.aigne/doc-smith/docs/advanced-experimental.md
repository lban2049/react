# Experimental APIs

This guide covers experimental and unstable APIs in React. These APIs are intended for testing and feedback purposes. They may change significantly or be removed in future releases without following the usual deprecation cycle. Use them with caution, especially in production environments.

For more stable advanced features, you might be interested in [Server vs. Client Environments](./advanced-server-vs-client.md) or [Caching](./advanced-caching.md).

## Server-Side Security with Taint APIs

When building applications with React Server Components, it's critical to prevent sensitive server-only data from accidentally being passed to the client. The Taint APIs provide a mechanism to mark specific data as "tainted," causing React to throw an error if it attempts to serialize this data in a client-bound payload.

This security feature is only available in server environments.

```d2
direction: down

Server: {
  "Server Component": {
    "1. Taint sensitive data": {
      shape: step
      "experimental_taintUniqueValue('api_key', ...)"
    }
    "2. Prepare props for Client Component" : { shape: step }
  }
}

"Serialization Boundary": {
  shape: hexagon
  "3. Check for tainted values"
}

Client: {
  "Client Component"
}

Server -> "Serialization Boundary": "Pass props"

subgraph {
  direction: right
  "Serialization Boundary" -- "Data is clean" --> Client: "4a. Send payload"
  "Serialization Boundary" -- "Tainted value detected" --> Server: "4b. Throw Error (Leak prevented)" {
    style.stroke: red
  }
}
```

### `experimental_taintUniqueValue`

This function taints a unique primitive value, such as a secret key or token. It can be used with strings, bigints, and ArrayBuffer views.

**Parameters**

| Name      | Type                                | Description                                                                                                                            |
|-----------|-------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `message` | `string` (optional)                 | A custom error message to be thrown if the tainted value is serialized. Defaults to a generic warning.                                 |
| `lifetime`| `object`                            | An object that holds the value. The taint is considered valid for the lifetime of this object.                                       |
| `value`   | `string` \| `bigint` \| `ArrayBufferView` | The unique, sensitive value to taint. It cannot be a general object or function.                                                       |

**Example**

```javascript
// In a Server Component or Server Action
import { experimental_taintUniqueValue } from 'react';

async function processUserData(user) {
  const userSecrets = { apiKey: process.env.USER_API_KEY };

  // Taint the API key to prevent it from ever leaving the server.
  // The 'userSecrets' object defines the lifetime of the taint.
  experimental_taintUniqueValue(
    'API key should not be sent to the client.',
    userSecrets,
    userSecrets.apiKey
  );

  // ... if userSecrets were passed to a Client Component, React would throw an error.
}
```

### `experimental_taintObjectReference`

This function taints an entire object or function reference. This is useful for things like database connections or session objects that should never be serialized.

**Parameters**

| Name      | Type                  | Description                                                                                             |
|-----------|-----------------------|---------------------------------------------------------------------------------------------------------|
| `message` | `string` (optional)   | A custom error message to be thrown if the tainted object is serialized. Defaults to a generic warning. |
| `object`  | `object` \| `function`  | The object or function reference to taint.                                                              |

**Example**

```javascript
// In a server-side module
import { experimental_taintObjectReference } from 'react';

// Imagine this is your database connection pool
const dbConnection = createDatabaseConnection();

// Taint the entire connection object.
experimental_taintObjectReference('Database connection cannot be serialized.', dbConnection);

export function getData() {
  // This function can use dbConnection safely on the server.
  // But if it or the connection were passed to the client, React would throw.
  return dbConnection.query('SELECT * FROM users');
}
```

## Declarative Rendering Postponement with `unstable_postpone`

The `unstable_postpone` function allows a Server Component to pause its rendering declaratively. When called, React stops the current render pass and waits for a new one to be initiated, at which point it will try to render again from the root. This can be useful in scenarios where some prerequisite for rendering is not yet met, and you prefer to wait instead of showing a `Suspense` fallback.

**Usage**

`unstable_postpone` is called with a single string argument that provides a reason for the postponement. This reason is used for debugging purposes.

```javascript
import { unstable_postpone as postpone } from 'react';

function FeatureGate({ featureFlag }) {
  if (!featureFlag.isLoaded) {
    // If the feature flag data isn't ready, postpone rendering this tree.
    // React will wait and retry the render later.
    postpone('Feature flags are not loaded yet.');
  }

  if (!featureFlag.isEnabled) {
    return null; // Don't render if the feature is disabled
  }

  return <MyNewFeature />;
}
```

## Other Experimental APIs

Several other APIs are available under `experimental` or `unstable` prefixes. They provide access to new capabilities that are still under active development.

| API                                | Description                                                                                 |
|------------------------------------|---------------------------------------------------------------------------------------------|
| `experimental_useOptimistic`       | An older alias for `useOptimistic`. Now stable, using this will produce a developer warning.|
| `unstable_Activity`                | Scopes components to transitions, preventing fallbacks from being shown outside the scope.|
| `unstable_SuspenseList`            | Coordinates the loading sequence of multiple `Suspense` boundaries.                         |
| `unstable_ViewTransition`          | A component to manage CSS View Transitions for SPA navigations.                             |
| `unstable_startGestureTransition`  | Starts a transition specifically for gesture-based interactions.                            |
| `unstable_useCacheRefresh`         | Provides a mechanism to refresh data in the React Cache.                                    |
| `unstable_getCacheForType`         | A server-only API to access a cache instance for a specific type.                           |

---

By exploring these APIs, you can get a glimpse into the future direction of React and provide valuable feedback. However, always be prepared for breaking changes when using them.

Next, you can learn more about how React handles different environments in [Server vs. Client Environments](./advanced-server-vs-client.md).