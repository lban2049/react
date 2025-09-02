# Context

Context 提供了一种在组件树中传递数据的方式，而无需在每一层手动向下传递 props。它旨在共享那些对于 React 组件树而言可以被视为“全局”的数据，例如当前已认证的用户、主题或首选语言。

当你有许多不同嵌套层级的组件都需要某个数据时，你可能会发现自己需要通过一些本身并不需要该数据的组件来传递 props。这种情况通常被称为“prop drilling”，它会降低组件的可复用性，并增加重构的难度。Context 解决了这个问题，它允许父组件将某些数据提供给其下组件树中的任何组件，无论嵌套多深，都无需显式地传递 prop。

### Context 如何工作

Context 的流程涉及三个主要部分：
1.  **`React.createContext()`**：创建一个 Context 对象。
2.  **`Context.Provider`**：一个向其后代组件提供 context 值的组件。
3.  **`Context.Consumer` 或 `useContext()`**：订阅 context 变化并接收其值的组件。

```d2
direction: down

"App": {
  label: "App"
  shape: rectangle
  style: {
    fill: "#f0f0f0"
  }

  "Provider": {
    label: "<ThemeContext.Provider value='dark'>"
    shape: rectangle
    style: {
      fill: "#c5e1a5"
    }

    "IntermediateComponent": {
      label: "中间组件\n(不需要 theme)"
      shape: rectangle
      style: {
        fill: "#fff9c4"
      }

      "ConsumingComponent": {
        label: "消费组件\n(通过 useContext 使用 theme)"
        shape: rectangle
        style: {
          fill: "#b3e5fc"
        }
      }
    }
  }
}

"Provider" -> "ConsumingComponent": "Context 值 ('dark') 直接传递" {
  style: {
    stroke-dash: 4
    animated: true
  }
}

"App"."Provider"."IntermediateComponent" -> "App"."Provider"."IntermediateComponent"."ConsumingComponent": "标准的 Prop 传递 (被 Context 绕过)" {
  style: {
    stroke: "#cccccc"
  }
}
```

## `createContext(defaultValue)`

此函数是创建新 context 的入口点。它返回一个包含 `Provider` 和 `Consumer` 组件的 context 对象。

**Parameters**

| Name | Type | Description |
|---|---|---|
| `defaultValue` | `T` | 当一个 consumer 没有在匹配的 `Provider` 内部渲染时，它将收到的默认值。这对于在不包装组件的情况下进行隔离测试非常有用。 |

**Returns**

一个 `ReactContext` 对象，包含以下属性：

| Property | Type | Description |
|---|---|---|
| `Provider` | `React.ComponentType` | 一个允许消费组件订阅 context 变化的组件。 |
| `Consumer` | `React.ComponentType` | 一个订阅 context 变化的组件。需要一个函数作为其子组件 (render prop)。 |

**Example**

在这里，我们为当前 UI 主题创建了一个 context，并将 `'light'` 作为默认值。

```javascript
import { createContext } from 'react';

// 只有当组件在组件树中没有找到匹配的 Provider 时，
// 才会使用这个默认值。
export const ThemeContext = createContext('light');
```

## `Context.Provider`

每个 Context 对象都附带一个 `Provider` 组件，允许消费组件订阅 context 的变化。

`Provider` 组件接受一个 `value` prop，用于传递给该 `Provider` 的后代消费组件。每当 `Provider` 的 `value` prop 发生变化时，所有作为其后代的 consumer 都会重新渲染。

**Props**

| Name | Type | Description |
|---|---|---|
| `value` | `T` | 提供给该 provider 所有后代 consumer 的值。 |
| `children` | `React.Node` | 能够访问 context 值的组件树部分。 |

**Example**

使用 provider 包装组件树，以使其中的任何组件都可以使用 theme 值。

```javascript
import React, { useState } from 'react';
import { ThemeContext } from './ThemeContext';
import Toolbar from './Toolbar';

function App() {
  const [theme, setTheme] = useState('dark');

  // 值 'dark' 将被传递给任何消费 ThemeContext 的后代组件
  return (
    <ThemeContext.Provider value={theme}>
      <Toolbar />
    </ThemeContext.Provider>
  );
}
```

## 消费 Context

读取 context 值有两种方法：现代的基于 hook 的方法和传统的 render-prop 组件。

### `useContext` (推荐)

The `useContext` hook 是在函数组件中消费 context 的现代化首选方法。它接受一个 context 对象（即 `React.createContext` 的返回值），并返回该 context 的当前值。

这使得消费 context 值变得简洁明了。如需完整指南，请参阅 [useContext Hook 文档](./hooks-other.md)。

**Example**

```javascript
import React, { useContext } from 'react';
import { ThemeContext } from './ThemeContext';

function ThemedButton() {
  // useContext 返回最近的 ThemeContext.Provider 的值
  const theme = useContext(ThemeContext);

  return <button style={{ background: theme === 'dark' ? '#333' : '#FFF' }}>A Themed Button</button>;
}
```

### `Context.Consumer`

`Context.Consumer` 是一个订阅 context 变化的组件。在类组件或无法使用 Hook 的旧代码库中，它非常有用。

它需要一个函数作为其子组件。该函数接收当前的 context 值并返回一个 React 节点。

**Example**

```javascript
import React from 'react';
import { ThemeContext } from './ThemeContext';

class ThemedButton extends React.Component {
  render() {
    return (
      <ThemeContext.Consumer>
        {theme => (
          <button style={{ background: theme === 'dark' ? '#333' : '#FFF' }}>
            A Themed Button (Class Component)
          </button>
        )}
      </ThemeContext.Consumer>
    );
  }
}
```

---

Context 是一个在应用程序中共享状态的强大工具。要了解其最常见的使用方式，请继续阅读 [`useContext` Hook](./hooks-other.md) 的文档。
