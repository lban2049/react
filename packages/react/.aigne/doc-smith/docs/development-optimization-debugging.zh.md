# 调试工具

在开发 React 应用程序时，了解组件行为和识别渲染问题非常重要。React 提供了专门用于开发环境的工具，例如 `useDebugValue` 和 `captureOwnerStack`，以帮助在开发过程中检查组件状态和追踪组件所有权。

这些工具主要用于开发环境，不包含在生产构建中，从而确保它们不会影响应用程序性能或包大小。

## useDebugValue

`useDebugValue` 是一个 React Hook，允许您在 React DevTools 中为自定义 Hook 显示自定义标签。这在构建复杂自定义 Hook 时特别有用，因为它可以在 DevTools 中直接提供对其内部状态或派生值的更清晰表示，使调试更直接。

此 Hook 仅在开发模式下运行，在生产环境中无效。

**参数**

| 名称 | 类型 | 描述 |
|---|---|---|
| `value` | `T` | 您希望在 React DevTools 中显示的值。这可以是任何 JavaScript 值。 |
| `formatterFn` | `?(value: T) => mixed` | 一个可选函数，在显示之前格式化 `value`。此函数接收 `value` 作为其参数，并应返回格式化后的输出。使用格式化函数可以将昂贵的格式化计算推迟到实际检查 Hook 时进行，从而在常规 DevTools 使用期间提高性能。 |

**使用示例**

考虑一个自定义 Hook `useFriendStatus`，它跟踪朋友的在线状态。如果没有 `useDebugValue`，DevTools 可能只会显示原始返回值，这可能不会立即具有描述性。通过使用 `useDebugValue`，您可以提供一个更有意义的标签。

```javascript
import React, { useState, useEffect, useDebugValue } from 'react';

function useFriendStatus(friendID) {
  const [isOnline, setIsOnline] = useState(null);

  // 用于演示，模拟 API 调用
  useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }
    // 假设 subscribeToFriendStatus 和 unsubscribeFromFriendStatus 在其他地方定义
    // subscribeToFriendStatus(friendID, handleStatusChange);
    // return () => unsubscribeFromFriendStatus(friendID, handleStatusChange);

    // 模拟延迟后状态更新
    const timeout = setTimeout(() => {
      setIsOnline(friendID % 2 === 0); // 示例逻辑
    }, 1000);
    return () => clearTimeout(timeout);
  }, [friendID]);

  // 使用 useDebugValue 在 DevTools 中显示自定义标签
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

// 使用 FriendListItem 的示例组件
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

在此示例中，当您在 React DevTools 中检查 `FriendListItem` 时，您将看到 `useFriendStatus` Hook 的自定义标签，例如“Status: Online”或“Status: Offline”，这提供了对朋友状态的即时洞察，而无需展开 Hook 的内部状态。

## captureOwnerStack

`captureOwnerStack` 是一个仅限开发的实用函数，用于捕获组件所有者堆栈。所有者堆栈是渲染当前组件的组件列表。这对于高级调试场景特别有用，例如在处理深度嵌套的组件树或复杂的渲染流时，识别哪个父组件负责渲染特定子组件。

此函数旨在供 React 的开发工具内部使用，或在开发模式下用于非常具体的调试需求。它在生产构建中不可用。

**返回**

| 名称 | 类型 | 描述 |
|---|---|---|
| `ownerStack` | `null \| string` | 一个字符串，表示 React 组件所有者的调用堆栈，如果函数在非开发环境之外调用或无法捕获堆栈，则为 `null`。 |

**使用注意事项**

通常，`captureOwnerStack` 由 React 内部机制调用，以提供详细的错误消息或在 React DevTools 等工具中填充调试信息。作为开发人员，您通常不会在应用程序代码中直接调用此函数。相反，您将利用 React DevTools 提供的调试功能，这些功能利用了此实用程序和类似的内部实用程序。

例如，如果您在开发过程中遇到“Invalid hook call”错误，React 通常会使用 `captureOwnerStack` 等机制来提供有用的堆栈跟踪，指向可能违反 Hook 规则的位置。React 内部代码库中的 `resolveDispatcher` 函数利用 `ReactSharedInternals.H` 来识别当前调度器，如果它为 `null`，它会记录一条详细的错误消息，该消息可能隐式使用或与所有者堆栈信息相关，以指导调试。

```javascript
// 内部 React 代码演示了如何解析调度器，
// 以及所有者堆栈上下文如何隐式用于错误消息。
// (这不是您通常会在应用程序中编写的代码)
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

// 实际的 captureOwnerStack 实现：
// 您通常不会直接调用它，但它在内部用于调试。
// import {captureOwnerStack as captureOwnerStackImpl} from './src/ReactClient';
// let captureOwnerStack: ?() => null | string;
// if (__DEV__) {
//   captureOwnerStack = captureOwnerStackImpl;
// }
// export {captureOwnerStack};

```

这些调试实用程序在 React 应用程序的开发阶段提供了宝贵的见解，从而可以更有效地识别和解决与组件相关的问题。它们是调试工具的内部 Hook，而不是直接用于应用程序逻辑的功能。

有关 React 编译器运行时如何通过自动化记忆化来优化应用程序性能的信息，请转到 [编译器运行时](./development-optimization-compiler-runtime.md) 部分。