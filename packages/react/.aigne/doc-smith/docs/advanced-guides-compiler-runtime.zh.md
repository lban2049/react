# 编译器运行时

React 编译器是一个实验性项目，也被称为“Forget”，旨在通过对 React 组件进行记忆化（memoizing）来自动优化它们，从而减少手动使用 `useMemo` 和 `useCallback` 等钩子的需求。为实现此目的，编译器可能会向代码的编译输出中注入某些运行时辅助函数。

本节为这些运行时辅助函数提供参考信息。需要明确的是，这些函数被视为编译器的实现细节。你不需要在应用程序代码中直接导入或使用它们。本信息旨在提供给高级用户、工具作者以及对编译器内部工作原理感兴趣的人。

## useMemoCache

编译器运行时提供的主要辅助函数是 `useMemoCache`。编译器使用此钩子来分配一个固定大小的数组，用作组件内记忆化值的缓存。编译器会根据对组件的分析来确定此缓存所需的大小。

该函数从 `react/compiler-runtime` 包中以 `c` 的名称导出。

### 参数

<x-field data-name="size" data-type="number" data-required="true" data-desc="在缓存数组中分配的槽位数。该值由 React 编译器根据组件内可记忆化表达式的数量自动确定。"></x-field>

### 返回值

<x-field data-name="cacheArray" data-type="Array<mixed>" data-desc="返回一个指定大小的稳定数组。编译器使用此数组在多次重新渲染之间存储记忆化的值。"></x-field>

### 概念性用法

你不会直接使用 `useMemoCache` 编写代码。React 编译器会将你的标准 React 代码转换为使用此钩子的优化版本。下面是一个关于此转换过程如何工作的概念性示例。


```javascript 你的原始代码 icon=logos:react
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

当 React 编译器处理此组件时，它会识别出两个可记忆化的表达式，并将代码转换为使用其运行时进行优化，从而无需手动调用 `useMemo`。

```javascript 假设的编译输出 icon=logos:react
// 这是一个简化的、概念性的编译器输出示例。
import { c as useMemoCache } from 'react/compiler-runtime';

function UserProfile({ user, permissions }) {
  // 编译器确定需要一个大小为 4 的缓存来存储值和依赖项。
  const $$cache = useMemoCache(4);
  let welcomeMessage;
  let canEditProfile;

  // 编译器生成的逻辑，用于检查依赖项并更新缓存。
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

如你所见，`useMemoCache` 提供了底层机制，使编译器能够执行自动记忆化，从而在无需额外手动操作的情况下提高组件性能。

---

要了解有关 React 中其他高级和实验性功能的更多信息，你可能会发现以下部分很有用：

<x-cards>
  <x-card data-title="实验性 API" data-icon="lucide:flask-conical" data-href="/advanced-guides/experimental">
    探索其他可用于测试和反馈的不稳定及实验性 API。
  </x-card>
  <x-card data-title="JSX 运行时" data-icon="lucide:code" data-href="/advanced-guides/jsx-runtime">
    了解现代 JSX 转换及其使用的运行时函数。
  </x-card>
</x-cards>