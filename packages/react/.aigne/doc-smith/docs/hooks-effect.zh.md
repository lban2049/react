# Effect Hook

Effect Hook 允许你在函数组件中执行副作用。副作用是指那些能够影响其他组件、并且无法在渲染期间完成的操作，例如获取数据、设置订阅或手动更改 DOM。

这些 Hook 提供了一种将组件与外部系统同步的方法。本节将介绍三种主要的 Effect Hook，每种 Hook 都针对组件生命周期中的特定时机而设计：

- `useEffect`：用于处理大多数副作用。它在渲染和浏览器绘制*之后*运行。
- `useLayoutEffect`：用于需要在 DOM 变更之后、浏览器绘制*之前*同步运行的 Effect。这对于测量布局很有用。
- `useInsertionEffect`：一个专门为 CSS-in-JS 库设计的 Hook，用于在 DOM 变更*之前*注入样式。

```d2
direction: down

"开始渲染": {
  shape: circle
}

"渲染阶段": "React 渲染组件"

"提交阶段": {
  "1. useInsertionEffect": "在 DOM 变更前触发"
  "2. DOM 变更": "React 更新 DOM"
  "3. useLayoutEffect": "在 DOM 变更后触发"
}

"浏览器绘制": "浏览器绘制屏幕"

"useEffect": "在绘制后触发"

"结束": {
  shape: circle
}

"开始渲染" -> "渲染阶段"
"渲染阶段" -> "提交阶段"
"提交阶段" -> "浏览器绘制"
"浏览器绘制" -> "useEffect"
"useEffect" -> "结束"
```

## useEffect

`useEffect` 是管理副作用最常用的 Hook。它在渲染提交到屏幕后异步运行，从而确保 Effect 代码不会阻塞浏览器的绘制过程。

### 语法

```javascript
useEffect(create, deps);
```

### 参数

| 参数 | 类型 | 描述 |
|---|---|---|
| `create` | `() => (() => void) \| void` | 一个包含副作用逻辑的函数。它可以选择性地返回一个清理函数，React 会在组件卸载前或 Effect 重新运行前执行该函数。 |
| `deps` | `Array<mixed> \| void \| null` | 一个依赖项数组。只有当其中一个依赖项自上次渲染以来发生变化时，Effect 才会重新运行。如果传入一个空数组 (`[]`)，Effect 只在初始渲染后运行一次。如果省略，Effect 会在每次渲染后运行。 |

### 示例：获取数据

```javascript
import React, { useState, useEffect } from 'react';

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    // 获取用户数据的 Effect
    const fetchUser = async () => {
      const response = await fetch(`https://api.example.com/users/${userId}`);
      const data = await response.json();
      setUser(data);
    };

    fetchUser();

    // 清理函数
    return () => {
      console.log(`Cleaning up effect for user ${userId}`);
      // 在这里你可以取消仍在进行中的 fetch 请求
    };
  }, [userId]); // 仅在 userId 更改时重新运行 Effect

  if (!user) {
    return <div>Loading...</div>;
  }

  return <h1>{user.name}</h1>;
}
```
在此示例中，组件在挂载时以及每当 `userId` prop 发生变化时都会获取用户数据。清理函数确保在组件卸载或 Effect 重新运行之前处理任何过时的操作。

## useLayoutEffect

`useLayoutEffect` 的签名与 `useEffect` 相同，但它在所有 DOM 变更完成后、浏览器绘制结果之前同步触发。这对于从 DOM 中读取布局信息并同步重新渲染以防止视觉上的“闪烁”非常有用。

### 语法

```javascript
useLayoutEffect(create, deps);
```

### 何时使用 `useLayoutEffect`

仅当你的 Effect 需要执行 DOM 测量并根据这些测量结果触发重新渲染时，才应使用 `useLayoutEffect`。例如，在内容渲染后计算工具提示的位置。将其用于像数据获取这样的标准 Effect 会因阻塞视觉更新而损害性能。

### 示例：测量元素布局

```javascript
import React, { useState, useLayoutEffect, useRef } from 'react';

function Tooltip({ children, targetRef }) {
  const tooltipRef = useRef(null);
  const [position, setPosition] = useState({ top: 0, left: 0 });

  useLayoutEffect(() => {
    if (targetRef.current && tooltipRef.current) {
      const targetRect = targetRef.current.getBoundingClientRect();
      const tooltipRect = tooltipRef.current.getBoundingClientRect();

      // 将工具提示定位在目标元素的上方
      setPosition({
        top: targetRect.top - tooltipRect.height - 5,
        left: targetRect.left + (targetRect.width / 2) - (tooltipRect.width / 2),
      });
    }
  }, [targetRef]); // 当目标发生变化时重新计算

  return (
    <div ref={tooltipRef} style={{ ...position, position: 'fixed' }}>
      {children}
    </div>
  );
}
```
在这里，`useLayoutEffect` 保证了当用户看到工具提示时，它已经处于正确计算的位置，从而避免了任何突兀的视觉跳动。

## useInsertionEffect

`useInsertionEffect` 是一个专为 CSS-in-JS 库作者设计的 Hook。它在 React 对 DOM 进行任何更改*之前*同步触发。这使得库可以将动态样式注入文档头部，并确保在浏览器为新 DOM 树计算布局之前这些样式已经可用。

应用程序开发者通常不应使用此 Hook。对于应用程序级别的逻辑，应优先使用 `useEffect` 或 `useLayoutEffect`。

### 语法

```javascript
useInsertionEffect(create, deps);
```

### 示例：基本样式注入

这是一个简化的概念性示例，说明了 CSS-in-JS 库可能如何使用此 Hook。

```javascript
import React, { useInsertionEffect } from 'react';

// 用于避免重复样式的全局缓存
const styleCache = new Map();

function useDynamicStyle(className, cssRule) {
  useInsertionEffect(() => {
    if (!styleCache.has(className)) {
      const styleElement = document.createElement('style');
      styleElement.innerHTML = `.${className} { ${cssRule} }`;
      document.head.appendChild(styleElement);
      styleCache.set(className, styleElement);
    }

    return () => {
      // 在真实的库中，清理逻辑会更复杂
    };
  }, [className, cssRule]);
}

function StyledButton() {
  useDynamicStyle('my-button', 'color: white; background-color: blue;');

  return <button className="my-button">Click Me</button>;
}
```
通过在 DOM 更新前运行，`useInsertionEffect` 确保了 `.my-button` 类在 `<button>` 元素渲染之前就已定义，从而防止了内容的样式闪烁。

## 总结

选择正确的 Effect Hook 取决于副作用的时机。对于大多数情况，`useEffect` 是正确的选择。当你需要在浏览器绘制前与 DOM 布局进行交互时，可以使用 `useLayoutEffect`。对于 CSS-in-JS 样式注入这一特定领域，`useInsertionEffect` 提供了必要的时机。

接下来，让我们在 [Ref Hook](./hooks-ref.md) 部分探讨如何在不触发重新渲染的情况下管理对值和 DOM 节点的引用。
