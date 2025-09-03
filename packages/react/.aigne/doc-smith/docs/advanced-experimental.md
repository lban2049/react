# Experimental APIs

**Warning: The APIs documented on this page are experimental and are not yet available in a stable release. They may change significantly or be removed entirely in a future version of React.**

This section provides an overview of experimental features that are under active development. They are intended for early adopters and library authors to experiment with and provide feedback. We strongly advise against using these APIs in production applications.

## Security: Tainting API

In server environments, it is crucial to prevent sensitive data, such as API keys or user session tokens, from being inadvertently passed to client-side code. The Tainting API is a server-only feature designed to create a security boundary, throwing an error if a "tainted" value is serialized and sent to the client.

This mechanism helps prevent data leaks when using React Server Components or Server Actions.

### `experimental_taintUniqueValue(message, lifetime, value)`

This function taints a unique primitive value, such as a secret token or key. React will prevent this specific value from being passed to any Client Component or Server Action closure.

**Parameters**

| Name | Type | Description |
|---|---|---|
| `message` | `string` | An optional custom error message to be thrown if the taint is violated. |
| `lifetime` | `object` | An object reference. The taint is removed when this object is garbage collected. This helps manage the memory used by the taint registry. |
| `value` | `string` \| `bigint` \| `$ArrayBufferView` | The unique, sensitive primitive value to taint. |

**Example: Tainting a User's API Key**

```javascript
// Located in a server-only file
import { experimental_taintUniqueValue } from 'react';
import { getUserData } from './database';

export async function getTaintedUserData(userId) {
  const user = await getUserData(userId);

  // This object's lifetime is tied to the request
  const requestLifetime = {}; 

  // Taint the user's secret API key
  experimental_taintUniqueValue(
    'API key must not be exposed to the client.',
    requestLifetime,
    user.apiKey
  );

  return user;
}

// In a Server Component:
async function UserProfile({ userId }) {
  const user = await getTaintedUserData(userId);

  // This is safe because `user.apiKey` is not being passed to the client.
  const serverSideData = await fetchDataWithKey(user.apiKey);

  return (
    // If you were to pass `user.apiKey` to ClientInfo, React would throw an error.
    <ClientInfo name={user.name} />
  );
}
```

### `experimental_taintObjectReference(message, object)`

This function taints an entire object or function reference. Any attempt to serialize this object and send it to the client will result in an error.

**Parameters**

| Name | Type | Description |
|---|---|---|
| `message` | `string` | An optional custom error message to be thrown if the taint is violated. |
| `object` | `object` \| `function` | The object or function reference to taint. |

**Example: Tainting a Database Connection**

```javascript
// Located in a server-only file
import { experimental_taintObjectReference } from 'react';
import { createDbConnection } from './db';

const db = createDbConnection();

// Taint the database connection object to prevent it from ever leaving the server.
experimental_taintObjectReference(
  'The database connection object cannot be sent to the client.',
  db
);

export default db;
```

## Rendering: `postpone(reason)`

The `postpone` function allows a React Server Component to interrupt its rendering process without causing a server error. When called, it signals to the React renderer that the component is not yet ready to render and that the rendering should be retried later. This is useful for scenarios where data is not yet available, and you prefer to wait rather than render a `Suspense` fallback.

It works by throwing a special object that the renderer catches and interprets as a signal to pause.

**Parameters**

| Name | Type | Description |
|---|---|---|
| `reason` | `string` | A descriptive string explaining why the rendering was postponed. This is used for debugging. |

**Example: Postponing for a Personalized Greeting**

```javascript
import { postpone } from 'react';
import { getPersonalizedContent } from './contentApi';

async function PersonalizedGreeting({ userId }) {
  // Fetch personalized content, which might be slow to generate initially.
  const content = await getPersonalizedContent(userId);

  if (content.status === 'PENDING') {
    // If the content isn't ready, postpone rendering.
    // React will hold the connection and retry rendering this component.
    postpone(`Personalized content for user ${userId} is not ready.`);
  }

  return <h1>{content.greeting}</h1>;
}
```

---

These experimental APIs provide powerful new capabilities for building secure and dynamic applications. As they mature, they may be integrated into the stable React API. For now, use them to explore and provide feedback. For production-ready features, please consult the main [API Reference](./api-reference.md).