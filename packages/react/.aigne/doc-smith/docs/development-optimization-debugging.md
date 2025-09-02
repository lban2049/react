# Debugging Utilities

When developing React applications, understanding component behavior and identifying rendering issues is important. React provides specific development-only utilities, such as `useDebugValue` and `captureOwnerStack`, to assist with inspecting component state and tracing component ownership during development.

These tools are primarily for use in development environments and are not included in production builds, ensuring they do not impact application performance or bundle size.

## useDebugValue

`useDebugValue` is a React Hook that allows you to display a custom label for your custom Hooks in React DevTools. This can be particularly useful when building complex custom Hooks, as it provides a clearer representation of their internal state or derived values directly in the DevTools, making debugging more straightforward.

This Hook runs only in development mode and has no effect in production.

**Parameters**

| Name | Type | Description |
|---|---|---| 
| `value` | `T` | The value you wish to display in React DevTools. This can be any JavaScript value. |
| `formatterFn` | `?(value: T) => mixed` | An optional function that formats the `value` before it's displayed. This function receives the `value` as its argument and should return the formatted output. Using a formatter function can defer expensive formatting calculations until the Hook is actually inspected, improving performance during regular DevTools usage. |

**Usage Example**

Consider a custom Hook `useFriendStatus` that tracks a friend's online status. Without `useDebugValue`, the DevTools might only show the raw return value, which may not be immediately descriptive. By using `useDebugValue`, you can provide a more meaningful label.

```javascript
import React, { useState, useEffect, useDebugValue } from 'react';

function useFriendStatus(friendID) {
  const [isOnline, setIsOnline] = useState(null);

  // For demonstration, simulating an API call
  useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }
    // Assume subscribeToFriendStatus and unsubscribeFromFriendStatus are defined elsewhere
    // subscribeToFriendStatus(friendID, handleStatusChange);
    // return () => unsubscribeFromFriendStatus(friendID, handleStatusChange);

    // Simulate status update after some delay
    const timeout = setTimeout(() => {
      setIsOnline(friendID % 2 === 0); // Example logic
    }, 1000);
    return () => clearTimeout(timeout);
  }, [friendID]);

  // Use useDebugValue to display a custom label in DevTools
  useDebugValue(isOnline ? 'Online' : 'Offline', isOnline => isOnline ? 'Status: Online' : 'Status: Offline');

  return isOnline;
}

function FriendListItem({ friend }) {
  const isOnline = useFriendStatus(friend.id);

  return (
    <li>
      {friend.name} - {isOnline === null ? 'Loading...' : (isOnline ? 'Online' : 'Offline')}
    </li>
  );
}

// Example component using FriendListItem
function FriendList() {
  const friends = [
    { id: 1, name: 'Alice' },
    { id: 2, name: 'Bob' },
  ];

  return (
    <ul>
      {friends.map(friend => (
        <FriendListItem key={friend.id} friend={friend} />
      ))}
    </ul>
  );
}
```

In this example, when you inspect `FriendListItem` in React DevTools, you will see a custom label like "Status: Online" or "Status: Offline" for the `useFriendStatus` Hook, providing immediate insight into the friend's status without needing to expand the Hook's internal state.

## captureOwnerStack

`captureOwnerStack` is a development-only utility function that captures the component owner stack. The owner stack is a list of components that rendered the current component. This is particularly useful for advanced debugging scenarios, such as when identifying which parent component is responsible for rendering a specific child, especially when dealing with deeply nested component trees or complex rendering flows.

This function is intended for internal use by React's development tools or for very specific debugging needs in development mode. It is not available in production builds.

**Returns**

| Name | Type | Description |
|---|---|---| 
| `ownerStack` | `null \| string` | A string representing the call stack of React component owners, or `null` if the function is called outside of a development environment or if the stack cannot be captured. |

**Usage Considerations**

Typically, `captureOwnerStack` is invoked by internal React mechanisms to provide detailed error messages or to populate debugging information in tools like React DevTools. As a developer, you would generally not call this function directly in your application code. Instead, you would leverage the debugging capabilities provided by React DevTools, which utilize this and similar internal utilities.

For instance, if you encounter an "Invalid hook call" error in development, React often uses mechanisms like `captureOwnerStack` to provide a helpful stack trace pointing to where the hook rule might have been violated. The `resolveDispatcher` function within React's internal code base leverages `ReactSharedInternals.H` to identify the current dispatcher, and if it's `null`, it logs a detailed error message that may implicitly use or be related to owner stack information to guide debugging.

```javascript
// Internal React code demonstrating how dispatcher is resolved,
// and implicitly how owner stack context might be used for error messages.
// (This is not code you would typically write in your application)
import ReactSharedInternals from 'shared/ReactSharedInternals';
import type {Dispatcher} from 'react-reconciler/src/ReactInternalTypes';

function resolveDispatcher() {
  const dispatcher = ReactSharedInternals.H;
  if (__DEV__) {
    if (dispatcher === null) {
      console.error(
        'Invalid hook call. Hooks can only be called inside of the body of a function component. This could happen for' +
          ' one of the following reasons:\n' +
          '1. You might have mismatching versions of React and the renderer (such as React DOM)\n' +
          '2. You might be breaking the Rules of Hooks\n' +
          '3. You might have more than one copy of React in the same app\n' +
          'See https://react.dev/link/invalid-hook-call for tips about how to debug and fix this problem.',
      );
    }
  }
  return ((dispatcher: any): Dispatcher);
}

// The actual captureOwnerStack implementation:
// You would not typically call this directly, but it's used internally for debugging.
// import {captureOwnerStack as captureOwnerStackImpl} from './src/ReactClient';
// let captureOwnerStack: ?() => null | string;
// if (__DEV__) {
//   captureOwnerStack = captureOwnerStackImpl;
// }
// export {captureOwnerStack};

```

These debugging utilities provide valuable insights during the development phase of your React applications, allowing for more efficient identification and resolution of component-related issues. They serve as internal hooks for debugging tools rather than features for direct application logic.

For information on how the React Compiler Runtime can optimize your application's performance by automating memoization, proceed to the [Compiler Runtime](./development-optimization-compiler-runtime.md) section.