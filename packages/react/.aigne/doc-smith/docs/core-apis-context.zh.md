# Context

Context 提供了一种在组件树中传递数据的方法，无需在每个层级手动传递 props。它旨在共享那些对于 React 组件树而言可以被视为“全局”的数据，例如当前已认证的用户、主题或偏好语言。

尽管将 context 用于所有状态管理可能很诱人，但请务必记住，这会降低组件的可重用性。对于更简单的情况，应首先考虑[组件组合](./core-apis-components-and-props.md)。然而，对于需要被不同嵌套层级的多个组件访问的状态管理，Context 是一种有效的解决方案。

## 核心概念

Context API 由三个主要部分组成：

1.  **`createContext`**：一个用于创建 context 对象的函数。
2.  **`Context.Provider`**：一个向其后代组件提供 context 值的组件。
3.  **`useContext` Hook / `Context.Consumer`**：组件订阅和读取 context 值的几种方式。

下图说明了 provider 如何直接将数据传递给深度嵌套的 consumer，从而绕过中间组件。

```d2
direction: down

"App": {
  shape: rectangle

  "ThemeContext.Provider value='dark'": {
    shape: package
    grid-columns: 1

    "Layout": {
      shape: rectangle
      label: "Layout\n(不需要主题)"
    }

    "ThemedButton": {
      shape: rectangle
      label: "ThemedButton\n(使用主题)"
    }

    "Layout" -> "ThemedButton": "渲染"
  }
}

"ThemeContext.Provider value='dark'" -> "ThemedButton": "直接提供 'dark' 值" {
  style.stroke-dash: 4
}
```

---

## API 参考

### `createContext(defaultValue)`

该函数创建一个 Context 对象。当 React 渲染一个订阅此 Context 对象的组件时，它会从组件树中上层最近的 `Provider` 中读取当前的 context 值。

`defaultValue` 参数**仅**在组件树中其上层没有匹配的 `Provider` 时使用。这对于在不封装组件的情况下进行独立测试很有帮助。

**Parameters**

| Name | Type | Description |
|---|---|---|
| `defaultValue` | `T` | 如果组件树中没有 provider，context 将使用的值。 |

**Returns**

一个包含 `Provider` 和 `Consumer` 属性的 context 对象。

**Example**

```javascript
// src/ThemeContext.js
import { createContext } from 'react';

// 默认值为 'light'
export const ThemeContext = createContext('light');
```

### `Context.Provider`

每个 Context 对象都附带一个 `Provider` React 组件，允许消费组件订阅 context 的变化。

`Provider` 组件接受一个 `value` prop，该 prop 会被传递给该 `Provider` 的所有后代消费组件。每当 `Provider` 的 `value` prop 发生变化时，其所有后代 consumer 都将重新渲染。

**Props**

| Name | Type | Description |
|---|---|---|
| `value` | `T` | 要传递给组件树深层所有消费组件的值。 |
| `children` | `ReactNode` | 可以访问 context 值的组件。 |

**Example**

```javascript
import React from 'react';
import { ThemeContext } from './ThemeContext';
import Toolbar from './Toolbar';

function App() {
  // 此 provider 内的任何组件现在都可以读取 'dark' 这个值
  return (
    <ThemeContext.Provider value="dark">
      <Toolbar />
    </ThemeContext.Provider>
  );
}
```

### 消费 Context

消费 context 值主要有两种方式。

#### `useContext(Context)`

读取 context 值的现代推荐方法是使用 `useContext` Hook。它很简洁，并且能自然地融入函数组件中。

该 hook 接受一个 context 对象（即 `React.createContext` 的返回值），并返回该 context 的当前值。

有关详细指南，请参阅 [`useContext` Hook 文档](./hooks-other.md)。

**Example**

```javascript
import React, { useContext } from 'react';
import { ThemeContext } from './ThemeContext';

function ThemedButton() {
  // useContext hook 从最近的 Provider 获取值
  const theme = useContext(ThemeContext);

  return (
    <button style={{ background: theme === 'dark' ? '#333' : '#FFF', color: theme === 'dark' ? 'white' : 'black' }}>
      当前主题是 {theme}
    </button>
  );
}
```

#### `Context.Consumer`

在类组件或无法使用 Hook 的场景中，可以使用 `Consumer` 组件。

它需要一个函数作为子组件（即“render prop”）。该函数接收当前的 context 值并返回一个 React 节点。

**Example**

```javascript
import React from 'react';
import { ThemeContext } from './ThemeContext';

class ThemedButton extends React.Component {
  render() {
    return (
      <ThemeContext.Consumer>
        {theme => (
          <button style={{ background: theme === 'dark' ? '#333' : '#FFF', color: theme === 'dark' ? 'white' : 'black' }}>
            当前主题是 {theme}
          </button>
        )}
      </ThemeContext.Consumer>
    );
  }
}
```

---

## 后续步骤

现在你已经了解如何使用 Context 在应用程序中传递数据，接下来可以探索用于处理组件子元素的工具。

<x-card data-title="Children 工具" data-icon="lucide:box" data-href="/core-apis/children-utilities" data-cta="了解更多">
  学习如何使用 React.Children API 辅助函数来处理 props.children 数据结构。
</x-card>