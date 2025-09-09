# Experimental APIs

Welcome to the cutting edge of React. This section covers experimental and unstable APIs that are available for testing and feedback. These features are still under development, and their behavior or signature might change in future releases. We strongly advise against using them in production environments unless you are prepared for potential breaking changes.

These APIs provide early access to new capabilities, such as advanced security controls and more granular rendering management.

## Tainting: Preventing Sensitive Data Leaks

When working with React Server Components, it's crucial to prevent sensitive server-side data from accidentally being passed to the client. The Taint APIs provide a mechanism to mark certain values or objects as "tainted," causing React to throw an error if an attempt is made to serialize them for the client.

This is a powerful security feature for building robust server-centric applications.

### `experimental_taintUniqueValue`

This function is used to taint unique, primitive values like secrets, API keys, or tokens. It helps ensure that specific strings or numbers cannot leave the server environment.

**Usage**

```javascript Server Component icon=logos:react
import { experimental_taintUniqueValue } from 'react';

async function getUserData(userId) {
  const userSecret = await getSecretFromVault(userId);

  // Create a lifetime object. When this object is garbage collected,
  // the taint on the value may be removed.
  const lifetime = {};

  // Taint the secret value. If this is ever passed to a Client Component,
  // React will throw an error.
  experimental_taintUniqueValue(
    'User secret must not be exposed to the client.',
    lifetime,
    userSecret
  );

  return { secret: userSecret };
}
```

**Parameters**

| Parameter | Type | Description |
|---|---|---|
| `message` | `string` | The error message to display if the tainted value is passed to the client. |
| `lifetime` | `object` | An object whose garbage collection lifetime is tied to the taint. When the object is collected, the taint may be removed. |
| `value` | `string \| bigint \| $ArrayBufferView` | The unique primitive value to taint. |


### `experimental_taintObjectReference`

This function taints an entire object or function reference. It's useful for marking complex objects like database connections or file handles as server-only.

**Usage**

```javascript Server Component icon=logos:react
import { experimental_taintObjectReference } from 'react';

async function getDatabaseConnection() {
  const dbConnection = await createDbConnection();

  // Taint the entire database connection object.
  // It can't be passed to a Client Component or used in a Server Action.
  experimental_taintObjectReference(
    'Database connection objects are server-only and cannot be serialized.',
    dbConnection
  );

  return dbConnection;
}
```

**Parameters**

| Parameter | Type | Description |
|---|---|---|
| `message` | `string` | The error message to display if the tainted object is passed to the client. |
| `object` | `object \| function` | The object or function reference to taint. |


## Postponing Rendering with `postpone`

The `unstable_postpone` function allows a Server Component to declaratively pause its rendering and wait for data without blocking the server thread. When called, it throws a special signal that React catches. React then shows the nearest `<Suspense>` fallback and will retry rendering the component later.

This is particularly useful for handling data dependencies that aren't wrapped in a Promise-based API.

**Usage**

```javascript Page with Postponed Component icon=logos:react
import { Suspense } from 'react';
import { unstable_postpone as postpone } from 'react';
import { dataCache } from './data';

function NewsFeed() {
  const articles = dataCache.get('articles');
  if (!articles) {
    // If articles are not in the cache, postpone rendering.
    // React will show the Suspense fallback and retry.
    postpone('News feed is not ready yet.');
  }

  return (
    <div>
      {articles.map(article => <p key={article.id}>{article.title}</p>)}
    </div>
  );
}

export default function App() {
  return (
    <Suspense fallback={<div>Loading news...</div>}>
      <NewsFeed />
    </Suspense>
  );
}
```

This provides a way to integrate non-standard data fetching patterns with React's streaming server rendering capabilities.

## Other Experimental APIs

Below is a list of other experimental or unstable APIs available in certain React builds. Their purpose and usage may vary.

| API | Environment | Description |
|---|---|---|
| `experimental_useOptimistic` | Client | A Hook for managing optimistic UI updates. It has since been promoted to `useOptimistic` in Canary and stable channels. Using the `experimental_` prefix will produce a console warning. |
| `experimental_useEffectEvent` | Client | A proposed Hook to extract non-reactive logic from `useEffect` to prevent it from re-running unnecessarily. |
| `unstable_Activity` | Client | A component to control the visibility and state of offscreen content, useful for features like virtualized lists or tab panels. |
| `unstable_SuspenseList` | Client / Server | A component that helps coordinate the loading order of multiple `<Suspense>` boundaries to create a more controlled and less chaotic loading experience. |
| `unstable_ViewTransition` | Client | An API to help orchestrate animated transitions between different views or UI states, integrating with the browser's View Transitions API. |
| `unstable_getCacheForType` | Server | A server-only function to access a request-scoped cache instance for a given type, useful for memoizing data across components during a single render pass. |

---

Exploring these APIs can provide insight into the future direction of React. As they are server-focused, you may find the following guides helpful for context.

<x-cards>
  <x-card data-title="Server vs. Client Environments" data-icon="lucide:server-cog" data-href="/advanced/server-vs-client">
    Learn the fundamental differences between React's two rendering environments and their respective capabilities.
  </x-card>
  <x-card data-title="Caching" data-icon="lucide:database" data-href="/advanced/caching">
    Dive into React's caching mechanisms, which are essential for building high-performance server-rendered applications.
  </x-card>
</x-cards>