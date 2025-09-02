# Hooks 参考

本节提供所有标准和实验性 React Hooks 的详细文档，用于构建交互式客户端应用程序。这些 Hook 对于管理状态、处理副作用、访问上下文以及优化 React 组件中的性能至关重要。

如需更广泛地了解其他客户端 API，请参阅[客户端 API](./client-apis.md) 部分。

## 标准 Hook

### useState

`useState` Hook 允许您向函数组件添加状态。当状态改变时，组件会重新渲染。

**签名**

| 参数 | 类型 | 描述 |
|---|---|---|
| `initialState` | `(() => S) \| S` | 初始状态值。它可以是直接的值，也可以是返回初始值的函数（用于惰性初始化）。 |

**返回值**

| 名称 | 类型 | 描述 |
|---|---|---|
| `[state, setState]` | `[S, Dispatch<BasicStateAction<S>>]` | 一个数组，包含当前状态值 (`S`) 和一个状态设置函数 (`setState`) 用于更新状态。 |

**示例**

```javascript
import React, { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>Click me</button>
    </div>
  );
}

// To use this component:
// <Counter />
```

此示例将 `count` 状态变量初始化为 `0`，并提供一个按钮来增加它，演示了如何在函数组件中声明和更新状态。

### useEffect

`useEffect` Hook 允许您在函数组件中执行副作用，例如数据获取、订阅或手动更改 DOM。效果在每次渲染后运行，但您可以使用依赖项控制它们何时重新运行。

**签名**

| 参数 | 类型 | 描述 |
|---|---|---|
| `create` | `() => (() => void) \| void` | 效果函数。它可以选择返回一个清理函数，该函数在组件卸载之前或效果重新运行之前运行。 |
| `deps` | `Array<mixed> \| void \| null` | 可选的依赖项数组。只有当此数组中的任何值发生变化时，效果才会重新运行。如果省略或提供空数组 `[]`，则效果在初次渲染后运行一次。 |

**返回值**

| 名称 | 类型 | 描述 |
|---|---|---|
| `void` | `void` | 此 Hook 不返回值。 |

**示例**

```javascript
import React, { useState, useEffect } from 'react';

function DocumentTitleUpdater() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    // Update the document title after every render
    document.title = `You clicked ${count} times`;

    // Optional cleanup function
    return () => {
      console.log('Cleanup for count:', count);
    };
  }, [count]); // Re-run effect only if count changes

  return (
    <div>
      <p>Current count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}

// To use this component:
// <DocumentTitleUpdater />
```

此示例更新文档标题以反映 `count` 状态。`useEffect` Hook 以 `[count]` 作为其依赖项数组，确保标题仅在 `count` 更改时更新，并且清理函数在重新运行或卸载之前进行日志记录。

### useContext

`useContext` Hook 允许您直接从函数组件订阅 React Context，从而更容易地在整个组件树中共享主题或用户认证状态等值，而无需进行 prop 逐层传递。

**签名**

| 参数 | 类型 | 描述 |
|---|---|---|
| `Context` | `ReactContext<T>` | 由 `React.createContext()` 创建的 Context 对象。 |

**返回值**

| 名称 | 类型 | 描述 |
|---|---|---|
| `value` | `T` | 树中调用组件上方最近的 `Context.Provider` 的当前上下文值。 |

**示例**

```javascript
import React, { createContext, useContext } from 'react';

// 1. Create a Context
const ThemeContext = createContext('light');

// 2. Component that consumes the Context
function ThemedButton() {
  const theme = useContext(ThemeContext);
  return <button style={{ background: theme === 'dark' ? 'black' : 'white', color: theme === 'dark' ? 'white' : 'black' }}>
    My {theme} Button
  </button>;
}

// 3. Component that provides the Context
function App() {
  return (
    <ThemeContext.Provider value="dark">
      <ThemedButton />
    </ThemeContext.Provider>
  );
}

// To use this component:
// <App />
```

此示例演示了如何创建 `ThemeContext`，`ThemedButton` 如何使用 `useContext` 消费 `theme` 值，以及 `App` 如何向其子组件提供 `dark` 主题值。

### useReducer

`useReducer` Hook 提供了 `useState` 的替代方案，用于管理涉及多个子值或下一个状态依赖于前一个状态的更复杂的状态逻辑。当状态转换复杂时，通常优先使用 `useReducer` 而不是 `useState`。

**签名**

| 参数 | 类型 | 描述 |
|---|---|---|
| `reducer` | `(S, A) => S` | 一个纯函数，接受当前状态和 action，并返回新状态。 |
| `initialArg` | `I` | 将传递给 `init` 函数（如果提供）或直接用作初始状态的初始值。 |
| `init` | `(I) => S` | 一个可选的初始化器函数，它从 `initialArg` 计算初始状态。这允许惰性初始化。 |

**返回值**

| 名称 | 类型 | 描述 |
|---|---|---|
| `[state, dispatch]` | `[S, Dispatch<A>]` | 一个数组，包含当前状态值 (`S`) 和一个 `dispatch` 函数，用于向 reducer 发送 action。 |

**示例**

```javascript
import React, { useReducer } from 'react';

const initialState = { count: 0 };

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 };
    case 'decrement':
      return { count: state.count - 1 };
    case 'reset':
      return initialState;
    default:
      throw new Error();
  }
}

function CounterWithReducer() {
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <>
      Count: {state.count}
      <button onClick={() => dispatch({ type: 'increment' })}>+</button>
      <button onClick={() => dispatch({ type: 'decrement' })}>-</button>
      <button onClick={() => dispatch({ type: 'reset' })}>Reset</button>
    </>
  );
}

// To use this component:
// <CounterWithReducer />
```

此示例使用 `useReducer` 管理 `count` 状态。`reducer` 函数定义了状态如何根据 'increment'、'decrement' 和 'reset' action 变化，并且 `dispatch` 用于发送这些 action。

### useRef

`useRef` Hook 允许您创建一个可变的 ref 对象，其 `.current` 属性可以在渲染之间持有可变值，而不会在值变化时引起重新渲染。它通常用于访问 DOM 元素或存储任何不会触发重新渲染的可变值。

**签名**

| 参数 | 类型 | 描述 |
|---|---|---|
| `initialValue` | `T` | 要分配给 ref 的 `.current` 属性的初始值。 |

**返回值**

| 名称 | 类型 | 描述 |
|---|---|---|
| `refObject` | `{current: T}` | 一个普通 JavaScript 对象，带有一个名为 `current` 的属性，初始化为 `initialValue`。 |

**示例**

```javascript
import React, { useRef } from 'react';

function TextInputWithFocusButton() {
  const inputEl = useRef(null);

  const onButtonClick = () => {
    // `current` points to the mounted text input element
    inputEl.current.focus();
  };

  return (
    <>
      <input ref={inputEl} type="text" />
      <button onClick={onButtonClick}>Focus the input</button>
    </>
  );
}

// To use this component:
// <TextInputWithFocusButton />
```

此示例使用 `useRef` 创建一个 `inputEl` ref，并将其附加到 `<input>` 元素。单击按钮使用 `inputEl.current.focus()` 以编程方式聚焦输入字段。

### useCallback

`useCallback` Hook 记忆化一个回调函数。它返回一个记忆化的回调版本，仅在其依赖项之一发生变化时才更改。这对于防止依赖引用相等性来防止重新渲染的子组件进行不必要的重新渲染非常有用。

**签名**

| 参数 | 类型 | 描述 |
|---|---|---|
| `callback` | `T` | 要记忆化的函数。 |
| `deps` | `Array<mixed> \| void \| null` | 依赖项数组。仅当此数组中的任何值发生变化时，`callback` 才会重新记忆化。如果提供空数组 `[]`，则回调函数被记忆化一次，并且永不改变。 |

**返回值**

| 名称 | 类型 | 描述 |
|---|---|---|
| `memoizedCallback` | `T` | `callback` 函数的记忆化版本。 |

**示例**

```javascript
import React, { useState, useCallback } from 'react';

function ParentComponent() {
  const [count, setCount] = useState(0);

  const handleClick = useCallback(() => {
    setCount(prevCount => prevCount + 1);
  }, []); // Empty dependency array means the function is memoized once

  return (
    <div>
      <p>Count: {count}</p>
      <ChildComponent onClick={handleClick} />
    </div>
  );
}

function ChildComponent({ onClick }) {
  console.log('ChildComponent rendered');
  return <button onClick={onClick}>Increment from Child</button>;
}

// To use this component:
// <ParentComponent />
```

在此示例中，`handleClick` 使用 `useCallback` 和空依赖项数组进行记忆化。这确保 `handleClick` 始终指向相同的函数实例，从而防止 `ParentComponent` 因其他原因重新渲染时 `ChildComponent` 不必要的重新渲染。

### useMemo

`useMemo` Hook 记忆化一个计算值。它只在依赖项之一发生变化时重新计算记忆化的值。这种优化有助于避免在每次渲染时进行昂贵的计算。

**签名**

| 参数 | 类型 | 描述 |
|---|---|---|
| `create` | `() => T` | 计算要记忆化的值的函数。 |
| `deps` | `Array<mixed> \| void \| null` | 依赖项数组。仅当此数组中的任何值发生变化时，`create` 函数才会重新执行。如果提供空数组 `[]`，则该值计算一次。 |

**返回值**

| 名称 | 类型 | 描述 |
|---|---|---|
| `memoizedValue` | `T` | `create` 函数产生的记忆化值。 |

**示例**

```javascript
import React, { useState, useMemo } from 'react';

function FibonacciCalculator({ n }) {
  const [inputN, setInputN] = useState(n);

  // An expensive calculation
  const calculateFibonacci = (num) => {
    console.log('Calculating Fibonacci...');
    if (num <= 1) return num;
    return calculateFibonacci(num - 1) + calculateFibonacci(num - 2);
  };

  // Memoize the result of calculateFibonacci
  const memoizedFib = useMemo(() => calculateFibonacci(inputN), [inputN]);

  return (
    <div>
      <input
        type="number"
        value={inputN}
        onChange={(e) => setInputN(parseInt(e.target.value, 10))}
      />
      <p>Fibonacci({inputN}): {memoizedFib}</p>
    </div>
  );
}

// To use this component:
// <FibonacciCalculator n={10} />
```

此示例使用 `useMemo` 记忆化 `calculateFibonacci` 的结果。`calculateFibonacci` 函数仅在 `inputN` 更改时运行，从而防止组件因其他原因重新渲染时重新计算。

### useImperativeHandle

`useImperativeHandle` Hook 自定义了在使用 `ref` 和 `forwardRef` 时暴露给父组件的实例值。它通常用于当您需要向父组件暴露一组有限的方法而不是完整的 DOM 元素时。

**签名**

| 参数 | 类型 | 描述 |
|---|---|---|
| `ref` | `{current: T \| null} \| ((inst: T \| null) => mixed) \| null \| void` | 从 `forwardRef` 传递的 `ref` 对象。 |
| `create` | `() => T` | 一个函数，返回您希望通过 ref 暴露的值。 |
| `deps` | `Array<mixed> \| void \| null` | 可选的依赖项数组。只有当此数组中的任何值发生变化时，`create` 函数才会重新执行。 |

**返回值**

| 名称 | 类型 | 描述 |
|---|---|---|
| `void` | `void` | 此 Hook 不返回值。 |

**示例**

```javascript
import React, { useRef, useImperativeHandle, forwardRef } from 'react';

const MyInput = forwardRef((props, ref) => {
  const inputRef = useRef();

  useImperativeHandle(ref, () => ({
    focus: () => {
      inputRef.current.focus();
    },
    setValue: (val) => {
      inputRef.current.value = val;
    }
  }));

  return <input type="text" ref={inputRef} />;
});

function ParentComponent() {
  const myInputRef = useRef();

  const handleFocus = () => {
    myInputRef.current.focus();
  };

  const handleSetValue = () => {
    myInputRef.current.setValue('Hello Imperative Handle');
  };

  return (
    <div>
      <MyInput ref={myInputRef} />
      <button onClick={handleFocus}>Focus Input</button>
      <button onClick={handleSetValue}>Set Value</button>
    </div>
  );
}

// To use this component:
// <ParentComponent />
```

此示例展示了 `MyInput` 如何使用 `useImperativeHandle` 通过 `myInputRef` 向其父组件暴露 `focus` 和 `setValue` 方法，允许父组件更精细地控制输入元素。

### useLayoutEffect

`useLayoutEffect` Hook 类似于 `useEffect`，但它在所有 DOM 突变后同步触发，在浏览器有机会进行绘制之前。这对于从 DOM 读取布局并同步重新渲染新布局非常有用，可以防止视觉不一致（例如，测量元素的大小）。

**签名**

| 参数 | 类型 | 描述 |
|---|---|---|
| `create` | `() => (() => void) \| void` | 效果函数。它可以选择返回一个清理函数。 |
| `deps` | `Array<mixed> \| void \| null` | 可选的依赖项数组。只有当此数组中的任何值发生变化时，效果才会重新运行。 |

**返回值**

| 名称 | 类型 | 描述 |
|---|---|---|
| `void` | `void` | 此 Hook 不返回值。 |

**示例**

```javascript
import React, { useState, useLayoutEffect, useRef } from 'react';

function Tooltip({ children, text }) {
  const [tooltipPosition, setTooltipPosition] = useState({});
  const buttonRef = useRef(null);

  useLayoutEffect(() => {
    if (buttonRef.current) {
      const rect = buttonRef.current.getBoundingClientRect();
      setTooltipPosition({
        top: rect.bottom + 5,
        left: rect.left,
      });
    }
  }, [children]); // Recalculate if children change

  return (
    <div style={{ position: 'relative', display: 'inline-block' }}>
      <button ref={buttonRef}>{children}</button>
      <div
        style={{
          position: 'absolute',
          background: 'black',
          color: 'white',
          padding: '5px',
          borderRadius: '3px',
          ...tooltipPosition,
        }}
      >
        {text}
      </div>
    </div>
  );
}

// To use this component:
// <Tooltip text="Hello World">Hover me</Tooltip>
```

此示例使用 `useLayoutEffect` 在 DOM 更新后立即测量按钮的位置，然后同步更新工具提示的位置，确保它正确显示而不会闪烁。

### useDebugValue

`useDebugValue` Hook 是一个仅用于开发的 Hook，可用于在 React DevTools 中为自定义 Hook 显示自定义标签。这有助于在开发过程中更轻松地检查自定义 Hook 的值。

**签名**

| 参数 | 类型 | 描述 |
|---|---|---|
| `value` | `T` | 您希望在 DevTools 中显示的值。 |
| `formatterFn` | `?(value: T) => mixed` | 一个可选的格式化函数。如果提供，将使用 `value` 调用格式化函数以进行格式化，然后再显示。这可以将昂贵的格式化操作推迟到检查 Hook 时。 |

**返回值**

| 名称 | 类型 | 描述 |
|---|---|---|
| `void` | `void` | 此 Hook 不返回值。 |

**示例**

```javascript
import React, { useState, useDebugValue } from 'react';

function useOnlineStatus() {
  const [isOnline, setIsOnline] = useState(true);

  useDebugValue(isOnline ? 'Online' : 'Offline');

  // ... (logic to update isOnline based on network status)

  return isOnline;
}

function NetworkStatusDisplay() {
  const online = useOnlineStatus();

  return <p>You are: {online ? 'Online' : 'Offline'}</p>;
}

// To use this component:
// <NetworkStatusDisplay />
```

在 React DevTools 中检查 `NetworkStatusDisplay` 时，`useDebugValue` 将在 `useOnlineStatus` Hook 旁边显示“Online”或“Offline”，提供对其状态的即时洞察。

### useTransition

`useTransition` Hook 返回一个状态值，指示转换是否正在进行中，以及一个 `startTransition` 函数，用于将 UI 更新标记为转换。转换是非紧急更新，可以被更紧急的更新中断。

**签名**

此 Hook 不接受任何参数。

**返回值**

| 名称 | 类型 | 描述 |
|---|---|---|
| `[isPending, startTransition]` | `[boolean, (callback: () => void, options?: StartTransitionOptions) => void]` | 一个数组，其中 `isPending` 是一个布尔值，指示转换是否处于活动状态，`startTransition` 是一个函数，用于包装非紧急状态更新。 |

**示例**

```javascript
import React, { useState, useTransition } from 'react';

function SearchBar() {
  const [inputValue, setInputValue] = useState('');
  const [displayValue, setDisplayValue] = useState('');
  const [isPending, startTransition] = useTransition();

  const handleChange = (e) => {
    setInputValue(e.target.value);

    // Mark this update as a transition
    startTransition(() => {
      setDisplayValue(e.target.value);
    });
  };

  return (
    <div>
      <input type="text" value={inputValue} onChange={handleChange} />
      {isPending && <p>Loading...</p>}
      <p>Search result for: {displayValue}</p>
    </div>
  );
}

// To use this component:
// <SearchBar />
```

此示例使用 `useTransition` 将 `setDisplayValue` 更新标记为非紧急。当 `isPending` 为 `true` 时，“Loading...”消息出现，表示转换正在进行中，允许输入保持响应。

### useDeferredValue

`useDeferredValue` Hook 推迟更新值，允许您优先处理更紧急的更新（例如在输入框中打字），同时显示 UI 另一部分的较旧的“陈旧”版本。这对于响应值但重新渲染成本高昂的 UI 元素非常有用。

**签名**

| 参数 | 类型 | 描述 |
|---|---|---|
| `value` | `T` | 您要推迟的值。 |
| `initialValue` | `T` | 在推迟期间使用的可选初始值。 |

**返回值**

| 名称 | 类型 | 描述 |
|---|---|---|
| `deferredValue` | `T` | 值的推迟版本。此值将比主要 `value` “滞后”可配置的量，从而允许 UI 在大量更新期间保持响应。 |

**示例**

```javascript
import React, { useState, useDeferredValue, useMemo } from 'react';

function SearchResults({ query }) {
  // Simulate an expensive filtering operation
  const filteredItems = useMemo(() => {
    console.log('Filtering items for:', query);
    // This would be your actual filtering logic
    return Array(1000).fill(0).map((_, i) => `Item ${i}: ${query}`);
  }, [query]);
  return (
    <div>
      {filteredItems.map((item, index) => (
        <div key={index}>{item}</div>
      ))}
    </div>
  );
}

function DeferredSearchInput() {
  const [query, setQuery] = useState('');
  const deferredQuery = useDeferredValue(query);

  return (
    <div>
      <input value={query} onChange={e => setQuery(e.target.value)} placeholder="Search..." />
      <SearchResults query={deferredQuery} />
    </div>
  );
}

// To use this component:
// <DeferredSearchInput />
```

此示例推迟了 `SearchResults` 使用的 `query`。在输入框中打字会立即更新 `query`，使输入框保持响应，而 `deferredQuery`（以及因此 `SearchResults`）仅在短暂停留或系统空闲时更新，从而防止因昂贵的过滤操作导致的 UI 卡顿。

### useId

`useId` Hook 生成一个唯一的、稳定的 ID，该 ID 保证在服务器和客户端上都是唯一的。这对于将标签与输入字段关联，或在水合 HTML 时为可访问性属性生成唯一 ID 非常有用。

**签名**

此 Hook 不接受任何参数。

**返回值**

| 名称 | 类型 | 描述 |
|---|---|---|
| `id` | `string` | 一个唯一的字符串 ID。 |

**示例**

```javascript
import React, { useId } from 'react';

function PasswordField() {
  const passwordHintId = useId();
  return (
    <div>
      <label htmlFor="password">Password:</label>
      <input id="password" type="password" aria-describedby={passwordHintId} />
      <p id={passwordHintId}>Password must be at least 8 characters long.</p>
    </div>
  );
}

// To use this component:
// <PasswordField />
```

此示例使用 `useId` 为密码提示生成一个唯一的 ID，然后通过 `aria-describedby` 将该提示与输入字段关联起来，以实现可访问性。

### useSyncExternalStore

`useSyncExternalStore` Hook 允许 React 组件订阅外部存储。它确保从存储的读取是一致的，并且更新是使用 React 的并发特性进行调度的，使其适用于与管理 React 自身状态系统之外状态的库（例如 Redux、RxJS）集成。

**签名**

| 参数 | 类型 | 描述 |
|---|---|---|
| `subscribe` | `(() => void) => () => void` | 一个函数，它接受一个回调 `() => void` 并将其订阅到存储。它必须返回一个 `unsubscribe` 函数来清理订阅。 |
| `getSnapshot` | `() => T` | 一个从存储读取当前值的函数。此函数必须是纯函数。 |
| `getServerSnapshot` | `() => T` | 一个可选函数，在服务器端渲染期间用于提供存储的初始快照。如果未提供，客户端将在水合期间渲染为空。 |

**返回值**

| 名称 | 类型 | 描述 |
|---|---|---|
| `value` | `T` | 从外部存储中检索到的当前值。 |

**示例**

```javascript
import React, { useSyncExternalStore, useEffect } from 'react';

// A simple external store
const myStore = {
  _value: 0,
  _listeners: new Set(),
  getValue() {
    return this._value;
  },
  setValue(newValue) {
    this._value = newValue;
    this._listeners.forEach(listener => listener());
  },
  subscribe(listener) {
    this._listeners.add(listener);
    return () => this._listeners.delete(listener);
  },
};

function ExternalStoreComponent() {
  const count = useSyncExternalStore(
    myStore.subscribe,
    myStore.getValue,
    myStore.getValue // Use getValue for server snapshot in this simple case
  );

  useEffect(() => {
    // Simulate external updates
    const interval = setInterval(() => {
      myStore.setValue(myStore.getValue() + 1);
    }, 1000);
    return () => clearInterval(interval);
  }, []);

  return <p>External Store Count: {count}</p>;
}

// To use this component:
// <ExternalStoreComponent />
```

此示例使用 `useSyncExternalStore` 将一个简单的 `myStore` 与 React 集成。组件订阅 `myStore` 的更改，当值更新时重新渲染，并显示来自外部存储的当前计数。

### use

`use` Hook 允许您读取资源的值，例如 Promise 或 Context。当与 Promise 一起使用时，它与 Suspense 集成，暂停渲染直到 Promise 解决。当与 Context 一起使用时，它提供了一种比 `useContext` 更简洁的方式来读取上下文值。

**签名**

| 参数 | 类型 | 描述 |
|---|---|---|
| `usable` | `Usable<T>` | 要读取的资源。这可以是 `Promise<T>` 或 `ReactContext<T>`。 |

**返回值**

| 名称 | 类型 | 描述 |
|---|---|---|
| `value` | `T` | `usable` 资源已解析的值。 |

**示例（与 Context）**

```javascript
import React, { createContext, use } from 'react';

const UserContext = createContext({ name: 'Guest' });

function UserDisplay() {
  const user = use(UserContext); // Read context directly
  return <p>Current User: {user.name}</p>;
}

function AppWithUserContext() {
  return (
    <UserContext.Provider value={{ name: 'Alice' }}>
      <UserDisplay />
    </UserContext.Provider>
  );
}

// To use this component:
// <AppWithUserContext />
```

此示例展示了 `UserDisplay` 如何使用 `use(UserContext)` 直接读取 `UserContext` 值，使上下文消费更加简化。

**示例（与 Promise 和 Suspense - 概念性）**

```javascript
import React, { Suspense, use } from 'react';

const fetchData = () => {
  return new Promise(resolve => {
    setTimeout(() => resolve('Data loaded!'), 2000);
  });
};

const dataPromise = fetchData(); // This promise should be created outside render or memoized

function DataComponent() {
  const data = use(dataPromise); // Suspends until dataPromise resolves
  return <p>{data}</p>;
}

function AppWithSuspense() {
  return (
    <Suspense fallback={<div>Loading data...</div>}>
      <DataComponent />
    </Suspense>
  );
}

// To use this component:
// <AppWithSuspense />
```

此概念性示例演示了 `use` 如何读取 Promise。当 `DataComponent` 渲染时，如果 `dataPromise` 未解析，React 会暂停渲染并显示最近的 `Suspense` 边界的 `fallback`，直到 Promise 解决。

### useOptimistic

`useOptimistic` Hook 允许您乐观地更新 UI，假设某个 action 会在实际异步操作完成之前成功。这能立即向用户提供反馈，改善感知性能。如果 action 后来失败，UI 可以恢复到其真实状态。

**签名**

| 参数 | 类型 | 描述 |
|---|---|---|
| `passthrough` | `S` | 初始乐观状态，通常是乐观更新之前的当前状态。 |
| `reducer` | `?(S, A) => S` | 一个可选的 reducer 函数。如果提供，它接受当前状态和 action (`A`)，并返回新的乐观状态。如果未提供，则 action 本身成为新的乐观状态。 |

**返回值**

| 名称 | 类型 | 描述 |
|---|---|---|
| `[optimisticState, addOptimistic]` | `[S, (A) => void]` | 一个数组，包含当前乐观状态 (`S`) 和一个 `addOptimistic` 函数，用于调度乐观更新。 |

**示例**

```javascript
import React, { useState, useOptimistic } from 'react';

function MessageList() {
  const [messages, setMessages] = useState([]);
  const [optimisticMessages, addOptimisticMessage] = useOptimistic(
    messages,
    (currentMessages, newMessage) => [...currentMessages, newMessage]
  );

  async function sendMessage(text) {
    const newMessage = { id: Date.now(), text, sending: true };
    addOptimisticMessage(newMessage); // Optimistically add message

    try {
      // Simulate API call
      await new Promise(resolve => setTimeout(resolve, 1000));
      setMessages(prev => [...prev, { ...newMessage, sending: false }]);
    } catch (error) {
      console.error('Failed to send message:', error);
      // Revert optimistic update if necessary (e.g., filter out the optimistic message)
      setMessages(messages); // Revert to original messages
    }
  }

  return (
    <div>
      <ul>
        {optimisticMessages.map(msg => (
          <li key={msg.id} style={{ opacity: msg.sending ? 0.5 : 1 }}>
            {msg.text} {msg.sending ? '(Sending...)' : ''}
          </li>
        ))}
      </ul>
      <button onClick={() => sendMessage(`Hello from ${Math.random().toFixed(2)}`)}>Send Message</button>
    </div>
  );
}

// To use this component:
// <MessageList />
```

此示例使用 `useOptimistic` 立即将新消息显示为“发送中...”，而实际的网络请求正在进行中。一旦请求完成，消息就会最终确定（如果出现错误，可能会恢复）。

### useActionState

`useActionState` Hook 允许您管理由表单提交或其他可能涉及异步操作的 action 触发的状态更新。它提供当前状态、一个调度 action 的函数以及一个指示 action 是否正在进行中的布尔值，使其成为使用服务器 action 处理表单的理想选择。

**签名**

| 参数 | 类型 | 描述 |
|---|---|---|
| `action` | `(Awaited<S>, P) => S` | action 函数。它接受当前状态和有效负载 (`P`)，并返回新状态 (`S`)。此函数可以是同步或异步的。 |
| `initialState` | `Awaited<S>` | action 的初始状态。 |
| `permalink` | `string` | action 的可选永久链接，用于跟踪或重新验证。 |

**返回值**

| 名称 | 类型 | 描述 |
|---|---|---|
| `[state, dispatch, isPending]` | `[Awaited<S>, (P) => void, boolean]` | 一个数组，包含：`state`（当前状态）、`dispatch`（一个带有效负载触发 action 的函数）和 `isPending`（一个布尔值，指示 action 是否正在运行）。 |

**示例**

```javascript
import React, { useActionState } from 'react';

async function createUser(prevState, formData) {
  const name = formData.get('name');
  const email = formData.get('email');
  console.log(`Creating user: ${name}, ${email}`);

  // Simulate API call
  await new Promise(resolve => setTimeout(resolve, 1500));

  if (!name || !email) {
    return { message: 'Name and Email are required.', success: false };
  }

  return { message: `User ${name} created successfully!`, success: true };
}

function UserForm() {
  const [state, formAction, isPending] = useActionState(createUser, { message: '', success: false });

  return (
    <form action={formAction}>
      <input type="text" name="name" placeholder="Name" disabled={isPending} />
      <br />
      <input type="email" name="email" placeholder="Email" disabled={isPending} />
      <br />
      <button type="submit" disabled={isPending}>
        {isPending ? 'Submitting...' : 'Create User'}
      </button>
      {state.message && <p style={{ color: state.success ? 'green' : 'red' }}>{state.message}</p>}
    </form>
  );
}

// To use this component:
// <UserForm />
```

此示例使用 `useActionState` 处理表单提交。`createUser` 是 action 函数，`formAction` 直接传递给表单的 `action` 属性。`isPending` 状态在提交期间禁用表单输入和按钮，`state.message` 显示结果。

## 实验性与不稳定 Hook

这些 Hook 目前是实验性或不稳定的，未来 React 版本中可能会有所更改。它们适用于高级用例或测试即将推出的功能。

### useInsertionEffect

`useInsertionEffect` Hook 专为 CSS-in-JS 库设计。它在 DOM 突变*之前*同步触发，允许库在进行任何布局计算或绘制之前将新样式注入 DOM。这确保了样式在元素被测量或渲染之前可用，从而防止 FOUC（无样式内容闪烁）等问题。

**签名**

| 参数 | 类型 | 描述 |
|---|---|---|
| `create` | `() => (() => void) \| void` | 效果函数，通常用于注入样式。它可以选择返回一个清理函数。 |
| `deps` | `Array<mixed> \| void \| null` | 可选的依赖项数组。当此数组中的任何值发生变化时，效果将重新运行。 |

**返回值**

| 名称 | 类型 | 描述 |
|---|---|---|
| `void` | `void` | 此 Hook 不返回值。 |

**示例（CSS-in-JS 的概念性示例）**

```javascript
import React, { useInsertionEffect } from 'react';

// Imagine a CSS-in-JS library's internal hook
function useStyleInjection(rule) {
  useInsertionEffect(() => {
    // This is where a CSS-in-JS library would inject a style tag
    const styleTag = document.createElement('style');
    styleTag.innerHTML = rule;
    document.head.appendChild(styleTag);

    return () => {
      document.head.removeChild(styleTag);
    };
  }, [rule]);
}

function StyledComponent() {
  useStyleInjection(`.my-red-text { color: red; }`);
  return <p className="my-red-text">This text is styled by useInsertionEffect (conceptually)</p>;
}

// To use this component:
// <StyledComponent />
```

此概念性示例说明了 CSS-in-JS 库如何使用 `useInsertionEffect` 在组件绘制*之前*将样式规则（`.my-red-text { color: red; }`）注入文档的 `<head>`，确保样式立即应用。

### experimental_useEffectEvent

`experimental_useEffectEvent` Hook 允许您定义一个“效果事件”，这是一个始终拥有最新 props 和 state 但不会导致效果重新运行的函数。这对于效果内部的事件处理程序非常有用，它们不需要每次更改时都触发效果重新运行，从而避免了常见的缺少依赖项的 lint 警告。

**签名**

| 参数 | 类型 | 描述 |
|---|---|---|
| `callback` | `F` | 要转换为效果事件的函数。此函数应包含不需要作为其父效果的依赖数组一部分的逻辑。 |

**返回值**

| 名称 | 类型 | 描述 |
|---|---|---|
| `eventFunction` | `F` | `callback` 函数的稳定引用。此返回的函数将始终访问组件的最新状态和 props，但其自身的标识在渲染之间是稳定的。 |

**示例**

```javascript
import React, { useEffect, useState, experimental_useEffectEvent } from 'react';

function ChatRoom({ roomId, theme }) {
  const [message, setMessage] = useState('');

  // Define an event handler that needs the latest message and theme
  const onSend = experimental_useEffectEvent(() => {
    // This function will always have the latest 'message' and 'theme'
    // without 'message' or 'theme' being dependencies of the effect below.
    console.log(`Sending '${message}' to room ${roomId} with theme ${theme}`);
    // Actual send logic here
  });

  useEffect(() => {
    // Setup chat connection
    console.log(`Connecting to chat room ${roomId}`);
    const connection = {
      connect: () => console.log('Connected!'),
      disconnect: () => console.log('Disconnected!'),
      onmessage: () => onSend() // Use the stable event function
    };
    connection.connect();

    return () => connection.disconnect();
  }, [roomId]); // roomId is a dependency, but onSend is not needed

  return (
    <div>
      <input
        value={message}
        onChange={e => setMessage(e.target.value)}
        placeholder="Type your message..."
      />
      <button onClick={() => onSend()}>Send</button>
      <p>Current Theme: {theme}</p>
    </div>
  );
}

// To use this component:
// <ChatRoom roomId="general" theme="dark" />
```

在此示例中，`onSend` 是一个效果事件。它可以访问最新的 `message` 和 `theme`，而无需将其包含在 `useEffect` 的依赖数组中。这避免了每次 `message` 或 `theme` 更改时效果不必要的重新运行，同时仍确保事件处理程序使用当前值。

### unstable_getCacheForType

`unstable_getCacheForType` 函数（严格来说不是 Hook，但与数据管理相关）允许您获取特定类型的缓存资源。它是用于管理 React 新缓存机制的内部实用程序。

**签名**

| 参数 | 类型 | 描述 |
|---|---|---|
| `resourceType` | `() => T` | 一个函数，当调用时，生成类型为 `T` 的资源。此函数也用作缓存的键。 |

**返回值**

| 名称 | 类型 | 描述 |
|---|---|---|
| `cachedResource` | `T` | 指定类型的缓存资源。如果尚未缓存，将调用 `resourceType()` 来生成并缓存资源。 |

**示例（概念性）**

```javascript
import React, { unstable_getCacheForType } from 'react';

// Imagine a resource producer
const createHttpClient = () => {
  console.log('Creating new HTTP client...');
  return { fetch: (url) => `Data from ${url}` };
};

function DataFetcher() {
  // Get or create a cached HTTP client instance
  const httpClient = unstable_getCacheForType(createHttpClient);

  const data = httpClient.fetch('https://api.example.com/data');

  return <p>{data}</p>;
}

// To use this component:
// <DataFetcher />
```

此概念性示例展示了如何使用 `unstable_getCacheForType` 来确保 `createHttpClient` 仅被调用一次，并且其结果在组件或渲染之间被缓存和重用，从而优化资源创建。

### unstable_useCacheRefresh

`unstable_useCacheRefresh` Hook 提供了一个函数，可用于显式刷新特定资源类型的缓存。这对于在突变之后或底层数据源发生更改时使缓存数据失效特别有用。

**签名**

此 Hook 不接受任何参数。

**返回值**

| 名称 | 类型 | 描述 |
|---|---|---|
| `refreshCache` | `<T>(?() => T, ?T) => void` | 用于刷新缓存的函数。第一个参数是 `resourceType` 函数（与 `unstable_getCacheForType` 中相同），第二个参数是要放入缓存的可选新值。 |

**示例（概念性）**

```javascript
import React, { unstable_getCacheForType, unstable_useCacheRefresh } from 'react';

const getUserData = (userId) => {
  console.log(`Fetching user ${userId} data...`);
  return new Promise(resolve => {
    setTimeout(() => resolve({ id: userId, name: `User ${userId}` }), 500);
  });
};

function UserDisplay({ userId }) {
  // This would be wrapped in Suspense and use() to read the promise
  const userData = unstable_getCacheForType(() => getUserData(userId));
  return <p>User: {userData.name}</p>;
}

function UserEditor({ userId }) {
  const refresh = unstable_useCacheRefresh();

  const handleUpdateUser = async () => {
    console.log('Updating user...');
    // Simulate API call to update user
    await new Promise(resolve => setTimeout(resolve, 1000));
    // After update, refresh the cache for this user's data
    refresh(() => getUserData(userId)); // Invalidate the specific cache entry
    alert('User updated, cache refreshed!');
  };

  return (
    <div>
      <UserDisplay userId={userId} />
      <button onClick={handleUpdateUser}>Update User</button>
    </div>
  );
}

// To use this component:
// <UserEditor userId="123" />
```

此概念性示例展示了 `unstable_useCacheRefresh` 如何用于在更新操作后使 `getUserData(userId)` 的缓存数据失效并刷新。这确保了 `UserDisplay` 在更新后将获取最新数据。

### unstable_useMemoCache

`unstable_useMemoCache` Hook 是一个内部实用程序，用于编译器优化的记忆化，特别供 React 编译器运行时使用。它提供一个固定大小的数组，可用于存储记忆化的值，允许编译器生成更高效的记忆化逻辑。

**签名**

| 参数 | 类型 | 描述 |
|---|---|---|
| `size` | `number` | 记忆化缓存数组的所需大小。 |

**返回值**

| 名称 | 类型 | 描述 |
|---|---|---|
| `cacheArray` | `Array<mixed>` | 用于存储记忆化值的固定大小数组。 |

**示例（编译器运行时的概念性示例）**

```javascript
// This hook is primarily for internal use by React Compiler Runtime.
// You typically won't use it directly in your application code.

// import { unstable_useMemoCache as c } from 'react'; // Renamed for compiler use

// function MyComponent(props) {
//   const cache = c(2); // Request a cache array of size 2

//   // The compiler would generate code like this:
//   // if (cache[0] !== props.value) {
//   //   cache[0] = props.value;
//   //   cache[1] = expensiveCalculation(props.value);
//   // }
//   // const result = cache[1];

//   // return <div>{result}</div>;
// }
```

此 Hook 通常不适用于直接应用程序使用。它充当 React 实验性编译器的构建块，允许它通过为记忆化提供专用、高效的内存空间来优化组件。

---

本节全面概述了 React 的客户端 Hook，涵盖了用于各种状态管理、副作用和性能优化场景的标准和实验性 API。理解这些 Hook 对于构建高效且可维护的 React 应用程序至关重要。

接下来，探索[组件和元素](./client-apis-components-elements.md)部分中的内置组件和元素实用程序。