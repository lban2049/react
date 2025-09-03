# Effect Hook

Effect Hook 允许你在函数组件中执行副作用。副作用是指那些能够影响其他组件、并且不能在渲染期间完成的操作，例如数据获取、设置订阅或手动更改 DOM。

本节将介绍 React 提供的三个主要的 Effect Hook，每个 Hook 都针对组件生命周期中的不同时机而设计：`useEffect`、`useLayoutEffect` 和 `useInsertionEffect`。

## useEffect

`useEffect` Hook 是处理副作用最常用的工具。它在渲染提交到屏幕且浏览器绘制出结果*之后*异步运行。这可以确保你的 effect 代码不会阻塞浏览器更新显示。

### 语法

```javascript
useEffect(
  create: () => (() => void) | void,
  deps: Array<mixed> | void | null,
): void
```

### 参数

| 名称     | 类型                | 描述                                                                                                                                                                                                                                                        | 
| :------- | :------------------ | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | 
| `create`   | `function`          | 一个包含副作用逻辑的函数。它可以选择性地返回一个清理函数，React 会在组件卸载或 effect 再次运行之前执行该函数。                                                                                           | 
| `deps`     | `Array<any>` (可选) | 一个依赖项数组。只有当其中一个依赖项自上次渲染以来发生变化时，effect 才会重新运行。如果省略，effect 会在每次渲染后都运行。如果提供一个空数组 `[]`，effect 只会在初始渲染后运行一次。 | 

### 示例：获取数据

以下是一个使用 `useEffect` 在组件挂载时从 API 获取数据的示例。

```javascript
import React, { useState, useEffect } from 'react';

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    // 此函数在组件渲染后运行
    const fetchUserData = async () => {
      const response = await fetch(`https://api.example.com/users/${userId}`);
      const data = await response.json();
      setUser(data);
    };

    fetchUserData();

    // 可选的清理函数
    return () => {
      // 如果组件卸载或 userId 发生变化，此代码将会运行。
      // 例如，你可以在此处取消一个待处理的 API 请求。
      console.log(`Cleaning up effect for user ${userId}`);
    };
  }, [userId]); // 仅当 userId 发生变化时，effect 才会重新运行

  if (!user) {
    return <div>Loading...</div>;
  }

  return <div>{user.name}</div>;
}
```

## useLayoutEffect

`useLayoutEffect` 的函数签名与 `useEffect` 相同，但它在所有 DOM 变更应用*之后*、浏览器有机会绘制这些变更*之前*同步触发。这对于从 DOM 读取布局（例如获取元素的尺寸或位置）并同步重新渲染以在用户看到变更前应用更改非常有用。

请谨慎使用此 Hook，因为同步执行可能会阻塞视觉更新。

### 语法

```javascript
useLayoutEffect(
  create: () => (() => void) | void,
  deps: Array<mixed> | void | null,
): void
```

### 示例：测量 DOM 元素

假设你需要根据一个按钮的尺寸来定位一个工具提示。`useLayoutEffect` 可以确保你在按钮渲染后、绘制前测量它，从而防止任何视觉闪烁。

```javascript
import React, { useState, useLayoutEffect, useRef } from 'react';

function Tooltip() {
  const buttonRef = useRef(null);
  const [tooltipWidth, setTooltipWidth] = useState(0);

  useLayoutEffect(() => {
    if (buttonRef.current) {
      // 在按钮进入 DOM 后、绘制前测量其宽度
      const width = buttonRef.current.offsetWidth;
      setTooltipWidth(width);
    }
  }, []); // 仅在初始布局后运行一次

  return (
    <div>
      <button ref={buttonRef}>Hover over me</button>
      <div style={{ width: tooltipWidth, marginTop: '10px', border: '1px solid black' }}>
        这个工具提示的宽度与按钮相同。
      </div>
    </div>
  );
}
```

## useInsertionEffect

`useInsertionEffect` 是一个专门的 Hook，它在进行任何 DOM 变更*之前*同步运行。其主要用例是供 CSS-in-JS 库向 DOM 注入动态样式。应用程序开发人员很少需要使用此 Hook。

由于它在 DOM 更新之前运行，因此是在不强迫浏览器在单个渲染周期内多次重新计算样式的情况下注入 `<style>` 标签的理想位置。

### 语法

```javascript
useInsertionEffect(
  create: () => (() => void) | void,
  deps: Array<mixed> | void | null,
): void
```

### 概念示例

这是一个关于样式库如何使用 `useInsertionEffect` 的简化示例。

```javascript
// 在一个假设的 CSS-in-JS 库内部
let ruleCache = new Set();

function useCSS(rule) {
  useInsertionEffect(() => {
    // 在浏览器看到新的 DOM 节点之前注入样式。
    if (!ruleCache.has(rule)) {
      const styleElement = document.createElement('style');
      styleElement.innerHTML = rule;
      document.head.appendChild(styleElement);
      ruleCache.add(rule);
    }
  }, [rule]);
}

// 使用该库的应用程序代码
function MyComponent() {
  useCSS(`.my-component { color: blue; }`);
  return <div className="my-component">这是蓝色的</div>;
}
```

## 比较与执行顺序

这些 Hook 之间的关键区别在于它们在 React 渲染-提交生命周期中的执行时机。下图说明了事件的顺序。

```d2
direction: down

"Render Phase": {
  shape: rectangle
  label: "React renders your components"
}

"Commit Phase": {
  shape: package
  grid-columns: 1
  style.stroke-dash: 2

  "1. Before DOM Mutations": {
    shape: rectangle
    
    "useInsertionEffect runs": {
      shape: oval
      style.fill: "#fffbe6"
    }
  }

  "2. DOM Mutations & Layout Calculation": {
    shape: rectangle

    "useLayoutEffect runs": {
      shape: oval
      style.fill: "#e6f7ff"
    }
  }

  "3. Browser Painting": {
    shape: rectangle
    label: "Browser paints the screen"
  }
  
  "4. After Paint": {
    shape: rectangle
    
    "useEffect runs": {
      shape: oval
      style.fill: "#f6ffed"
    }
  }
}

"Render Phase" -> "Commit Phase": "Triggers commit"
"Commit Phase"."1. Before DOM Mutations" -> "Commit Phase"."2. DOM Mutations & Layout Calculation"
"Commit Phase"."2. DOM Mutations & Layout Calculation" -> "Commit Phase"."3. Browser Painting"
"Commit Phase"."3. Browser Painting" -> "Commit Phase"."4. After Paint"

```

| Hook                 | 时机                                                    | 用例                                                    |
| -------------------- | --------------------------------------------------------- | ----------------------------------------------------------- |
| `useInsertionEffect` | 同步，在 DOM 变更前                         | 为 CSS-in-JS 库注入样式。                   |
| `useLayoutEffect`    | 同步，在 DOM 变更后、浏览器绘制前    | 测量 DOM 元素，同步重新渲染。             |
| `useEffect`          | 异步，在渲染提交且浏览器绘制后 | 数据获取、订阅以及大多数其他副作用。  |

---

理解不同的 Effect Hook 可以让你精确而高效地管理副作用。要了解如何直接与 DOM 节点或组件实例交互，请继续阅读下一节关于 [Ref Hooks](./hooks-ref.md) 的内容。