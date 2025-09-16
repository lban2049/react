# Server Environment

The `react/server` entry point is designed specifically for server-side environments, such as when using React Server Components. It provides a curated set of React APIs that are safe and optimized for server execution, along with powerful features for data caching and security.

This package excludes client-only APIs like `useState` or `useEffect` that rely on browser environments and interactivity. Instead, it focuses on rendering, data fetching patterns, and preventing sensitive data from being inadvertently exposed to the client.

## Available APIs

The following APIs are exported from the `react/server` package. Many of these are familiar from the standard `react` package, but are guaranteed to be safe for server use.

| Export                 | Type                      | Description                                                                                                   |
| ------------------------ | ------------------------- | ------------------------------------------------------------------------------------------------------------- |
| `Children`               | Object                    | Utilities for dealing with `props.children` (`map`, `forEach`, `count`, `toArray`, `only`).                   |
| `Fragment`               | Component                 | A component for rendering multiple elements without a wrapper DOM node.                                       |
| `Profiler`               | Component                 | Measures rendering performance of a React tree for DEV mode.                                                  |
| `StrictMode`             | Component                 | A tool for highlighting potential problems in an application (DEV-only).                                      |
| `Suspense`               | Component                 | Lets you display a fallback UI until its children have finished loading. Essential for data fetching.         |
| `cache`                  | Function                  | A server-only function to memoize data-fetching functions.                                                    |
| `cacheSignal`            | Function                  | Used in conjunction with `cache` for more advanced cache management.                                          |
| `cloneElement`           | Function                  | Clones and returns a new React element using an element as the starting point.                                |
| `createElement`          | Function                  | Creates and returns a new React element of the given type.                                                    |
| `createRef`              | Function                  | Creates a ref object.                                                                                         |
| `forwardRef`             | Function                  | Lets a component expose a DOM node to its parent component with a ref.                                        |
| `isValidElement`         | Function                  | Verifies if the object is a React element.                                                                    |
| `lazy`                   | Function                  | Lets you defer loading a component's code until it's rendered for the first time.                             |
| `memo`                   | Function                  | A higher-order component for memoizing a component's render output.                                           |
| `use`                    | Hook                      | A hook for reading the value of a resource, like a Promise or context.                                        |
| `useId`                  | Hook                      | A hook for generating unique IDs that are stable across the server and client.                                |
| `useCallback`            | Hook                      | Returns a memoized callback function.                                                                         |
| `useDebugValue`          | Hook                      | Can be used to display a label for custom hooks in React DevTools.                                            |
| `useMemo`                | Hook                      | Returns a memoized value.                                                                                     |
| `version`                | String                    | The current version of React.                                                                                 |
| `captureOwnerStack`      | Function                  | A DEV-only utility for capturing the component stack.                                                         |

## Server-Specific Features

Beyond the standard APIs, the server environment introduces specialized features for performance and security.

### Caching with `cache`

The `cache` function is a crucial utility for optimizing data fetching in React Server Components. It wraps a data-fetching function (e.g., a database query or an API call) and memoizes its result. This ensures that if the same function is called with the same arguments multiple times within a single server render pass, the underlying data fetch only occurs once.

```javascript Server-Side Data Fetching icon=logos:javascript
import { cache } from 'react/server';
import db from './database';

export const getUser = cache(async (id) => {
  console.log(`Fetching user ${id} from the database...`);
  const user = await db.users.find({ where: { id } });
  return user;
});

async function UserProfile({ id }) {
  const user = await getUser(id);
  return <div>{user.name}</div>;
}

async function UserAvatar({ id }) {
  // This call will be memoized and will not trigger a new database query
  // if called with the same `id` as UserProfile in the same render.
  const user = await getUser(id);
  return <img src={user.avatarUrl} alt={user.name} />;
}
```

### Security with Data Tainting

Data tainting is a security mechanism designed to prevent sensitive information from leaking from the server to the client. When you "taint" a value, you mark it as unsafe to be serialized and sent to a Client Component or used in a Server Action closure. If React detects an attempt to serialize a tainted value, it will throw an error, protecting your application from potential vulnerabilities.

This feature is enabled via the `enableTaint` feature flag.

#### `taintUniqueValue(message, lifetime, value)`

This function taints a unique primitive value, such as a secret token or a password hash. It's designed for values that are globally unique and should never be exposed.

<x-field data-name="message" data-type="string" data-required="false" data-desc="An optional error message to show if the tainted value is leaked."></x-field>
<x-field data-name="lifetime" data-type="object" data-required="true" data-desc="An object that holds the value. When this object is garbage-collected, the taint is removed."></x-field>
<x-field data-name="value" data-type="string | bigint | ArrayBufferView" data-required="true" data-desc="The unique primitive or binary value to taint."></x-field>

```javascript Tainting a Secret Token icon=logos:javascript
import { taintUniqueValue } from 'react/experimental'; // Actual import path may vary

async function getUserSession(request) {
  const session = await getSessionFromCookies(request.cookies);
  const secretToken = session.internalToken;

  // The `session` object defines the lifetime of the taint.
  taintUniqueValue(
    'Internal session token should not be sent to the client.',
    session,
    secretToken
  );

  return session;
}
```

#### `taintObjectReference(message, object)`

This function taints an entire object or function reference. This is useful for marking complex objects, like a database connection instance or a configuration object containing multiple secrets, as unsafe to pass to the client.

<x-field data-name="message" data-type="string" data-required="false" data-desc="An optional error message to show if the tainted object is leaked."></x-field>
<x-field data-name="object" data-type="object | function" data-required="true" data-desc="The object or function reference to taint."></x-field>

```javascript Tainting a Database Connection icon=logos:javascript
import { taintObjectReference } from 'react/experimental'; // Actual import path may vary

function getDBConnection() {
  const dbConnection = createConnection(process.env.DATABASE_URL);

  // Mark the entire connection object as unsafe to pass to the client.
  taintObjectReference('Database connection cannot be serialized.', dbConnection);

  return dbConnection;
}
```

## Internal Exports

The server environment also exports `__SERVER_INTERNALS_DO_NOT_USE_OR_WARN_USERS_THEY_CANNOT_UPGRADE`. As its name strongly suggests, this is an internal object used by React and its associated libraries. It provides access to shared server state, including the registries for data tainting. Application developers should never use this object directly, as it is unstable and subject to change without notice.

---

By providing these specialized tools, the `react/server` environment empowers developers to build performant and secure server-rendered applications with React. For more details on the common APIs, please see the full [API Reference](./api-reference.md).