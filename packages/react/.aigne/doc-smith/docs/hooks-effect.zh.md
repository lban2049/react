# Effect Hooks

Effect Hooks 允许组件执行副作用，使其能够与 React 控制范围之外的系统进行交互和同步。副作用包括获取数据、设置订阅或手动操作 DOM 等行为。

React 提供了三个用于处理副作用的主要 Hook，每个 Hook 都在组件生命周期的不同时间点运行，以应对特定的使用场景。

```d2
direction: down

Render-Phase: {
  label: "渲染阶段"
  shape: rectangle
  "1. React 渲染组件"
}

Commit-Phase: {
  label: "提交阶段"
  shape: rectangle
  grid-columns: 1

  "2. React 将变更提交到 DOM"

  useInsertionEffect-run: {
    label: "3. `useInsertionEffect` 运行\n（适用于 CSS-in-JS 库）"
  }

  useLayoutEffect-run: {
    label: "4. `useLayoutEffect` 运行\n（在绘制前读取布局）"
  }
}

Browser-Paint: {
  label: "浏览器绘制"
  shape: rectangle
  "5. 浏览器绘制屏幕"
}

useEffect-Execution: {
  label: "useEffect 执行"
  shape: rectangle
  "6. `useEffect` 运行\n（适用于大多数副作用）"
}

Render-Phase -> Commit-Phase: "触发"
Commit-Phase -> Browser-Paint: "同步"
Browser-Paint -> useEffect-Execution: "异步"
```

---

## `useEffect`

`useEffect` Hook 是处理副作用最常用的工具。它在 React 完成组件渲染且浏览器绘制屏幕*之后*运行，从而确保副作用代码不会阻塞视觉更新。

**签名**

```javascript
useEffect(
  create: () => (() => void) | void,
  deps: Array<mixed> | void | null,
): void
```

**参数**

| Parameter | Type | Description |
|---|---|---|
| `create` | `function` | 包含副作用逻辑的函数。该函数可以视情况返回一个清理函数。 |
| `deps` | `Array<mixed>` \| `null` \| `undefined` | 依赖项数组。仅当其中某个依赖项自上次渲染以来发生变化时，Effect 才会重新运行。如果省略，Effect 将在每次渲染后运行。如果提供一个空数组 `[]`，Effect 将只运行一次。 |

**用法**

`useEffect` 非常适合用于数据获取、设置事件监听器或任何其他异步操作。

**示例：获取数据**

```javascript
import React, { useState, useEffect } from 'react';

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    // 此函数包含副作用。
    async function fetchUserData() {
      const response = await fetch(`https://api.example.com/users/${userId}`);
      const data = await response.json();
      setUser(data);
    }

    fetchUserData();

    // 可选：返回一个清理函数。
    return () => {
      // 此代码在组件卸载时或 Effect 重新运行前执行。
      // 例如，用于取消一个正在进行的请求。
      console.log(`Cleaning up effect for user ${userId}`);
    };
  }, [userId]); // 仅在 userId 变更时，Effect 才会重新运行。

  if (!user) {
    return <div>Loading...</div>;
  }

  return <h1>{user.name}</h1>;
}
```

---

## `useLayoutEffect`

`useLayoutEffect` 的签名与 `useEffect` 相同，但它在所有 DOM 变更*之后*、浏览器重绘屏幕*之前*同步触发。这对于从 DOM 读取布局信息并同步触发重新渲染非常有用。

**签名**

```javascript
useLayoutEffect(
  create: () => (() => void) | void,
  deps: Array<mixed> | void | null,
): void
```

**用法**

当您需要执行 DOM 测量（例如获取元素的滚动位置或尺寸）并在用户看到任何视觉不一致之前触发另一次渲染时，应使用此 Hook。由于它是同步的，可能会阻塞绘制，因此应尽可能优先使用 `useEffect`。

**示例：测量元素高度**

```javascript
import React, { useState, useLayoutEffect, useRef } from 'react';

function Tooltip() {
  const ref = useRef(null);
  const [tooltipHeight, setTooltipHeight] = useState(0);

  useLayoutEffect(() => {
    // 在 tooltip 元素渲染到 DOM 后，测量其高度。
    if (ref.current) {
      const height = ref.current.offsetHeight;
      setTooltipHeight(height);
      console.log('Measured height:', height);
    }
  }, []); // 在初始渲染后运行一次。

  return (
    <div>
      <div ref={ref} style={{ position: 'absolute', top: '-9999px' }}>
        This is a tooltip with some content.
      </div>
      <p>The tooltip's calculated height is: {tooltipHeight}px</p>
    </div>
  );
}
```

---

## `useInsertionEffect`

`useInsertionEffect` 是一个专门的 Hook，它在 React 对 DOM 进行任何更改*之前*同步运行。其主要使用场景是供 CSS-in-JS 库在计算布局前将样式注入 DOM。

**此 Hook 主要面向库的作者。在应用程序代码中，您很可能不需要使用它。**

**签名**

```javascript
useInsertionEffect(
  create: () => (() => void) | void,
  deps: Array<mixed> | void | null,
): void
```

**用法**

CSS-in-JS 库可以使用此 Hook 注入具有最高优先级的样式标签，以确保在 `useLayoutEffect` 运行以读取布局信息之前，这些样式已经可用。

**概念示例**

```javascript
// 在一个假设的 CSS-in-JS 库内部
let ruleCache = new Map();

function useCSS(rule) {
  useInsertionEffect(() => {
    if (!ruleCache.has(rule)) {
      const styleElement = document.createElement('style');
      styleElement.textContent = rule;
      document.head.appendChild(styleElement);
      ruleCache.set(rule, styleElement);
    }
  }, [rule]);
}
```

---

### 总结

根据副作用所需的执行时机来选择 Effect Hook。

| Hook | 执行时机 | 常见用例 |
|---|---|---|
| `useEffect` | 异步，在渲染和绘制后 | 数据获取、订阅、计时器。默认选择。 |
| `useLayoutEffect` | 同步，在渲染后但在绘制前 | 测量 DOM 元素，需要在下次绘制前计算的动画。 |
| `useInsertionEffect` | 同步，在 DOM 变更前 | **适用于库作者：**为 CSS-in-JS 库注入关键样式。 |

接下来，我们将探讨如何管理那些不会触发重新渲染的值和 DOM 节点的引用。请继续阅读 [Ref Hooks](./hooks-ref.md) 章节。