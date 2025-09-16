# Compiler Runtime

The React Compiler, an experimental project also known as 'Forget', is designed to automatically optimize React components by memoizing them, reducing the need for manual use of hooks like `useMemo` and `useCallback`. To achieve this, the compiler may inject certain runtime helper functions into the compiled output of your code.

This section provides reference information for these runtime helpers. It's important to understand that these are considered implementation details of the compiler. You should not need to import or use them directly in your application code. The information is provided for advanced users, tooling authors, and those curious about the compiler's inner workings.

## useMemoCache

The primary helper function provided by the compiler runtime is `useMemoCache`. This hook is used by the compiler to allocate a fixed-size array that serves as a cache for memoized values within a component. The compiler determines the necessary size of this cache based on its analysis of the component.

This function is exported as `c` from the `react/compiler-runtime` package.

### Parameters

<x-field data-name="size" data-type="number" data-required="true" data-desc="The number of slots to allocate in the cache array. This is determined automatically by the React Compiler based on the number of memoizable expressions within a component."></x-field>

### Returns

<x-field data-name="cacheArray" data-type="Array<mixed>" data-desc="Returns a stable array of the specified size. The compiler uses this array to store memoized values across re-renders."></x-field>

### Conceptual Usage

You will not write code using `useMemoCache` directly. Instead, the React Compiler will transform your standard React code into an optimized version that utilizes this hook. Below is a conceptual example of how this transformation might work.


```javascript Your Original Code icon=logos:react
import { useMemo } from 'react';

function UserProfile({ user, permissions }) {
  const welcomeMessage = useMemo(() => {
    console.log('Recalculating welcome message...');
    return `Welcome, ${user.name}!`;
  }, [user.name]);

  const canEditProfile = useMemo(() => {
    console.log('Recalculating permissions...');
    return permissions.includes('edit_profile');
  }, [permissions]);

  return (
    <div>
      <h1>{welcomeMessage}</h1>
      {canEditProfile && <button>Edit Profile</button>}
    </div>
  );
}
```

When the React Compiler processes this component, it identifies two memoizable expressions and transforms the code to use its runtime for optimization, eliminating the manual `useMemo` calls.

```javascript Hypothetical Compiled Output icon=logos:react
// This is a simplified, conceptual example of the compiler's output.
import { c as useMemoCache } from 'react/compiler-runtime';

function UserProfile({ user, permissions }) {
  // The compiler determines a cache of size 4 is needed for values and dependencies.
  const $$cache = useMemoCache(4);
  let welcomeMessage;
  let canEditProfile;

  // Compiler-generated logic to check dependencies and update the cache.
  if ($$cache[0] !== user.name) {
    welcomeMessage = `Welcome, ${user.name}!`;
    $$cache[0] = user.name;
    $$cache[1] = welcomeMessage;
  } else {
    welcomeMessage = $$cache[1];
  }

  if ($$cache[2] !== permissions) {
    canEditProfile = permissions.includes('edit_profile');
    $$cache[2] = permissions;
    $$cache[3] = canEditProfile;
  } else {
    canEditProfile = $$cache[3];
  }

  return (
    <div>
      <h1>{welcomeMessage}</h1>
      {canEditProfile && <button>Edit Profile</button>}
    </div>
  );
}
```

As you can see, `useMemoCache` provides the underlying mechanism that enables the compiler to perform its automatic memoization, making your components more performant without additional manual effort.

---

To learn more about other advanced and experimental features in React, you may find the following sections useful:

<x-cards>
  <x-card data-title="Experimental APIs" data-icon="lucide:flask-conical" data-href="/advanced-guides/experimental">
    Explore other unstable and experimental APIs that are available for testing and feedback.
  </x-card>
  <x-card data-title="JSX Runtime" data-icon="lucide:code" data-href="/advanced-guides/jsx-runtime">
    Understand the modern JSX transform and the runtime functions it uses.
  </x-card>
</x-cards>