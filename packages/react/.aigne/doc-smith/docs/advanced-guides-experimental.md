# Experimental APIs

This section provides documentation for unstable APIs and features available in experimental builds of React. These APIs are subject to change or removal in future releases without a standard deprecation cycle. Please use them with caution, primarily for testing and feedback purposes, and avoid relying on them in production environments.

These features are at the cutting edge of React's development and offer a glimpse into future capabilities.

## Hooks

### `experimental_useOptimistic`

The `experimental_useOptimistic` hook provides a way to apply temporary, "optimistic" state changes to the UI. This is useful for actions that are expected to succeed, allowing the UI to update instantly while the actual asynchronous operation completes in the background. If the operation fails, the UI can revert to its previous state.

**Note:** As of recent Canary releases, `useOptimistic` has been stabilized. You should remove the `experimental_` prefix. This alias is provided for backward compatibility and will be removed in a future release.

**Signature**

```typescript
function experimental_useOptimistic<S, A>(
  passthroughState: S,
  updateFn: (currentState: S, optimisticValue: A) => S
): [S, (optimisticValue: A) => void];
```

**Example**

```javascript MessageForm.js icon=logos:javascript
import { experimental_useOptimistic as useOptimistic } from 'react';

function MessageForm({ messages, sendMessage }) {
  const [optimisticMessages, addOptimisticMessage] = useOptimistic(
    messages,
    (currentState, newMessage) => [
      ...currentState,
      { text: newMessage, sending: true },
    ]
  );

  async function formAction(formData) {
    const message = formData.get('message');
    addOptimisticMessage(message);
    await sendMessage(message); // Asynchronously send the message
  }

  return (
    <div>
      {optimisticMessages.map((msg, index) => (
        <div key={index}>
          {msg.text}
          {msg.sending && <small> (Sending...)</small>}
        </div>
      ))}
      <form action={formAction}>
        <input type="text" name="message" placeholder="Type a message..." />
        <button type="submit">Send</button>
      </form>
    </div>
  );
}
```
In this example, when a user sends a message, it appears in the list immediately with a "(Sending...)" indicator. Once the `sendMessage` promise resolves, React will update the state with the final data from the server, removing the optimistic message and replacing it with the confirmed one.

### `experimental_useEffectEvent`

This experimental hook is designed to extract non-reactive logic from `useEffect`. It creates a function with a stable identity that can be called from within an effect without being included in its dependency array. This prevents the effect from re-running unnecessarily when the event handler's logic changes, which is a common source of bugs.

**Signature**

```typescript
function experimental_useEffectEvent<T extends Function>(callback: T): T;
```

**Example**

```javascript ChatRoom.js icon=logos:javascript
import { useEffect, useState, experimental_useEffectEvent as useEffectEvent } from 'react';

function ChatRoom({ roomId, theme }) {
  const [messages, setMessages] = useState([]);

  // The onMessage function's logic depends on `theme`,
  // but we don't want the effect to re-subscribe when `theme` changes.
  const onMessage = useEffectEvent(message => {
    console.log(`New message with ${theme} theme: ${message}`);
    setMessages(prev => [...prev, message]);
  });

  useEffect(() => {
    const connection = createConnection(roomId);
    connection.on('message', onMessage);

    return () => connection.disconnect();
    // The effect only re-runs if `roomId` changes.
  }, [roomId]);

  return <div>...</div>;
}

```

## Transitions and UI

These APIs provide more granular control over UI transitions and component visibility.

| API | Description |
| --- | --- |
| `unstable_ViewTransition` | A component to help orchestrate view transitions, often used with the native CSS View Transitions API. |
| `unstable_startGestureTransition` | A specialized version of `startTransition` for updates initiated by user gestures (e.g., drag, swipe), helping React prioritize them appropriately. |
| `unstable_addTransitionType` | Associates a string identifier with an ongoing transition. This can be used by other features like `unstable_ViewTransition` to apply specific styles or behaviors. It must be called within a `startTransition` callback. |
| `unstable_Activity` | A component that controls the visibility of offscreen content, allowing you to keep a component's state alive even when it is not currently visible. |
| `unstable_SuspenseList` | A component that coordinates the loading order of multiple `<Suspense>` boundaries, allowing you to control how content is revealed (e.g., `forwards`, `together`). |

**Example: Using `unstable_addTransitionType`**

```javascript icon=logos:javascript
import { startTransition, unstable_addTransitionType } from 'react';

function navigate(url) {
  startTransition(() => {
    // Signal that this state update is part of a 'navigation' transition.
    unstable_addTransitionType('navigation');
    // Update state to trigger the page change.
    setCurrentPage(url);
  });
}
```

## Rendering and Caching

These APIs offer low-level control over the rendering process and React's caching mechanisms.

| API | Description |
| --- | --- |
| `unstable_postpone` | A function that, when called during render, tells React to skip rendering the current component tree and show a `<Suspense>` fallback instead. This is useful for de-prioritizing rendering when data or resources are not yet available. |
| `unstable_useCacheRefresh` | A hook that returns a function to invalidate the React Cache and trigger a re-render. This is essential for data-fetching patterns where you need to refetch or refresh data. |
| `unstable_getCacheForType` | A low-level function to access a specific cache instance based on a provided type (e.g., a function or context). |

**Example: Using `unstable_postpone`**

```javascript icon=logos:javascript
import { Suspense } from 'react';
import { unstable_postpone as postpone } from 'react';

function ProfileDetails({ userPromise }) {
  try {
    const user = userPromise.read(); // Reads data from a promise-based resource
    return <h1>{user.name}</h1>;
  } catch (promise) {
    if (typeof promise.then === 'function') {
      // If data is still loading, suspend.
      throw promise;
    } else {
      // If there was a different kind of error, postpone rendering.
      postpone('Failed to fetch user details.');
    }
  }
}

function App() {
  return (
    <Suspense fallback={<div>Loading or postponed...</div>}>
      <ProfileDetails />
    </Suspense>
  );
}
```

## Server-Side Security

These server-only functions are security features designed to prevent sensitive data from being accidentally passed from Server Components to Client Components.

### `experimental_taintUniqueValue`

Taints a primitive value, such as a secret key or token. If a tainted value is passed as a prop to a Client Component, React will throw an error.

### `experimental_taintObjectReference`

Taints an object or function reference, such as a database connection or a server-only utility. This prevents server-side handles and logic from leaking to the client.

**Example**

```javascript UserData.server.js icon=logos:javascript
// This is a Server Component
import { experimental_taintUniqueValue, experimental_taintObjectReference } from 'react';
import { getDbConnection } from './database';
import ClientComponent from './ClientComponent';

async function UserData({ userId }) {
  const apiKey = process.env.API_KEY;
  const db = getDbConnection();

  // Taint sensitive data to prevent it from leaving the server.
  experimental_taintUniqueValue('Do not expose API Key to the client', apiKey);
  experimental_taintObjectReference('Database connection cannot be passed to client', db);

  const userData = await db.query(`SELECT id, name FROM users WHERE id = ${userId}`);

  // This is safe: only serializable, non-tainted data is passed.
  return <ClientComponent user={userData} />;

  // This would throw an error because `apiKey` is tainted:
  // return <ClientComponent user={userData} apiKey={apiKey} />;
}
```