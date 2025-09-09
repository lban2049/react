# Effect Hook

Effect Hook 允许你在函数组件中执行副作用。副作用是指那些能够影响其他组件、且无法在渲染期间完成的操作，例如数据获取、设置订阅或手动更改 DOM。

React 提供了三个主要的 Hook 来管理 Effect，每个 Hook 根据其在渲染周期中的执行时机而有不同的用途。

<x-cards data-columns="3">
  <x-card data-title="useEffect" data-icon="lucide:play-circle">
    最常用的副作用 Hook。它在渲染提交到屏幕后异步运行，因此不会阻塞绘制。
  </x-card>
  <x-card data-title="useLayoutEffect" data-icon="lucide:ruler">
    在所有 DOM 变更之后、浏览器绘制之前同步触发。适用于从 DOM 读取布局信息并同步触发重新渲染的场景。
  </x-card>
  <x-card data-title="useInsertionEffect" data-icon="lucide:syringe">
    在 React 对 DOM 进行更改之前同步运行。这适用于非常特定的用例，例如 CSS-in-JS 库注入样式。
  </x-card>
</x-cards>

---

## useEffect

`useEffect` 是处理大多数副作用的首选 Hook。通过将其执行推迟到浏览器绘制之后，可以确保你的副作用不会降低用户体验。

你传递给 `useEffect` 的函数就是副作用本身。你可以选择性地从副作用中返回一个“清理”函数，React 会在组件卸载或副作用重新运行之前执行该函数。

### 参数

| 参数 | 类型 | 描述 |
|---|---|---|
| `create` | `() => (() => void) \| void` | 一个包含副作用逻辑的函数。它可以选择性地返回一个清理函数。 |
| `deps` | `Array<mixed> \| void \| null` | 依赖项数组。只有当其中一个依赖项自上次渲染以来发生变化时，副作用才会重新运行。如果省略，副作用将在每次渲染后运行。如果传入一个空数组 `[]`，副作用仅在初始渲染后运行一次。 |

### 示例：获取数据

此示例展示了如何从 API 获取数据并更新组件的状态。每当 `userId` prop 发生变化时，副作用会重新运行。

```javascript Fetching Data with useEffect icon=logos:javascript
import React, { useState, useEffect } from 'react';

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    setLoading(true);
    const fetchUserData = async () => {
      const response = await fetch(`https://api.example.com/users/${userId}`);
      const data = await response.json();
      setUser(data);
      setLoading(false);
    };

    fetchUserData();

    // 依赖项数组 [userId] 确保此副作用
    // 仅在 userId prop 更改时运行。
  }, [userId]);

  if (loading) {
    return <div>Loading...</div>;
  }

  return <div>{user.name}</div>;
}
```

### 示例：带清理的订阅

此示例展示了如何订阅浏览器事件并使用清理函数取消订阅，以防止内存泄漏。

```javascript Event Listener with Cleanup icon=logos:javascript
import React, { useState, useEffect } from 'react';

function MousePosition() {
  const [position, setPosition] = useState({ x: 0, y: 0 });

  useEffect(() => {
    const handleMouseMove = (e) => {
      setPosition({ x: e.clientX, y: e.clientY });
    };

    window.addEventListener('mousemove', handleMouseMove);

    // 返回一个清理函数
    return () => {
      window.removeEventListener('mousemove', handleMouseMove);
    };
  }, []); // 空数组表示此副作用在挂载时运行一次，在卸载时进行清理

  return (
    <p>
      Mouse X: {position.x}, Y: {position.y}
    </p>
  );
}
```

---

## useLayoutEffect

`useLayoutEffect` 的签名与 `useEffect` 相同，但它在所有 DOM 变更完成之后、浏览器绘制更改之前同步触发。这使其适用于需要从 DOM 读取布局（例如获取元素的尺寸）然后在用户看到任何视觉不一致之前同步触发重新渲染的任务。

**警告：** 由于 `useLayoutEffect` 是同步的，它可能会阻塞视觉更新。尽可能优先使用 `useEffect` 以避免性能损失。

### 参数

| 参数 | 类型 | 描述 |
|---|---|---|
| `create` | `() => (() => void) \| void` | 一个包含副作用逻辑的函数。它可以选择性地返回一个清理函数。 |
| `deps` | `Array<mixed> \| void \| null` | 用于控制副作用何时重新运行的依赖项数组。 |

### 示例：测量 DOM 节点

假设你需要根据按钮的宽度来定位一个工具提示。`useLayoutEffect` 确保你在按钮渲染后、用户看到它之前测量按钮，从而防止工具提示可能短暂出现在错误位置的闪烁现象。

```javascript Positioning a Tooltip icon=logos:javascript
import React, { useState, useLayoutEffect, useRef } from 'react';

function TooltipButton() {
  const buttonRef = useRef(null);
  const [tooltipWidth, setTooltipWidth] = useState(0);

  useLayoutEffect(() => {
    if (buttonRef.current) {
      // 从 DOM 中读取按钮的宽度
      const width = buttonRef.current.offsetWidth;
      // 在浏览器绘制之前同步更新状态
      setTooltipWidth(width);
    }
  }, []); // 仅在初始布局后运行一次

  return (
    <div>
      <button ref={buttonRef}>Hover over me</button>
      <div style={{ width: tooltipWidth, border: '1px solid black', marginTop: '5px' }}>
        This tooltip has the same width as the button.
      </div>
    </div>
  );
}
```

---

## useInsertionEffect

`useInsertionEffect` 是一个专门的 Hook，它在任何 DOM 变更*之前*同步运行。其主要用例是供 CSS-in-JS 库在浏览器执行布局计算之前将样式规则注入 DOM。这确保了在渲染 React 组件时样式定义可用，从而防止与样式相关的闪烁。

**注意：** 应用程序开发人员很少需要使用此 Hook。它主要面向库的作者。你无法在 `useInsertionEffect` 内部访问 DOM `ref`。

### 参数

| 参数 | 类型 | 描述 |
|---|---|---|
| `create` | `() => (() => void) \| void` | 一个包含样式插入逻辑的函数。它可以选择性地返回一个清理函数。 |
| `deps` | `Array<mixed> \| void \| null` | 用于控制副作用何时重新运行的依赖项数组。 |

### 示例：概念性样式注入

此示例说明了 CSS-in-JS 库可能如何使用 `useInsertionEffect` 将 `<style>` 标签注入文档的 `<head>` 中。

```javascript Conceptual CSS-in-JS Usage icon=logos:javascript
import { useInsertionEffect } from 'react';

let injectedRules = new Set();

function useCSS(rule) {
  useInsertionEffect(() => {
    // 这会在浏览器有机会看到 DOM 更新之前运行。
    if (!injectedRules.has(rule)) {
      injectedRules.add(rule);
      const styleElement = document.createElement('style');
      styleElement.innerHTML = rule;
      document.head.appendChild(styleElement);

      // 可选：返回一个清理函数以移除样式
      return () => {
        document.head.removeChild(styleElement);
        injectedRules.delete(rule);
      };
    }
  }, [rule]);
}

function MyStyledComponent() {
  // 库会对此进行抽象
  useCSS('.my-class { color: blue; }');
  return <div className="my-class">This is a styled component.</div>;
}
```

---

现在你已经了解了如何处理副作用，你可能需要直接与 DOM 节点交互或将 ref 转发给子组件。在下一节中学习如何操作。

➡️ [下一节：Ref Hooks](./hooks-ref.md)