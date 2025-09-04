# Context

Context 提供了一种在组件树中传递数据的方法，而无需在每一层手动传递 props。它旨在共享那些对于 React 组件树而言可以被视为“全局”的数据，例如当前已认证的用户、主题或偏好语言。

虽然 Context 是一个强大的功能，但应谨慎使用，因为它可能使组件复用变得更具挑战性。在许多情况下，props 逐层传递或组件组合是更简单、更合适的解决方案。在使用 Context 之前，请考虑是否可以通过[状态提升](https://react.dev/learn/sharing-state-between-components)来解决问题。

## 核心概念

Context API 主要围绕三个部分：`createContext`、`Provider` 和 `Consumer`。

```d2
direction: down

App: {
  shape: rectangle
  label: "App 组件"

  Context-Provider: {
    label: "<MyContext.Provider value={...}>"
    shape: package

    IntermediateComponent: {
      shape: rectangle
      label: "中间组件\n(Props 不会在此处传递)"

      DeeplyNestedComponent: {
        shape: rectangle
        label: "深度嵌套的组件\n(消费 context 值)"
      }
    }
  }
}

Context-Provider -> DeeplyNestedComponent: "直接提供值" {
  style.stroke-dash: 4
}
```

### 1. `createContext`

该函数创建一个 Context 对象。当 React 渲染订阅了此 Context 对象的组件时，它将从组件树中上层最近的匹配 `Provider` 读取当前的 context 值。

`defaultValue` 参数**仅**在组件在其上层树中没有匹配的 Provider 时使用。这对于在不包裹组件的情况下独立测试组件很有帮助。

**API 签名**

```javascript
const MyContext = React.createContext(defaultValue);
```

**实现细节**

`createContext` 函数会初始化一个 context 对象，该对象包含多个内部属性，其中包括 `Provider` 和 `Consumer` 组件。

```javascript
// src/ReactContext.js 的简化视图

export function createContext<T>(defaultValue: T): ReactContext<T> {
  const context: ReactContext<T> = {
    $$typeof: REACT_CONTEXT_TYPE,
    _currentValue: defaultValue,
    // ... 其他内部字段
    Provider: null, // 将被设置为 context 对象本身
    Consumer: null, // 将被设置为一个 consumer 对象
  };

  context.Provider = context;
  context.Consumer = {
    $$typeof: REACT_CONSUMER_TYPE,
    _context: context,
  };

  return context;
}
```

### 2. `Context.Provider`

每个 Context 对象都附带一个 `Provider` React 组件，它允许消费组件订阅 context 的变化。provider 接受一个 `value` prop，该 prop 会被传递给作为此 `Provider` 后代的消费组件。当 `Provider` 的 `value` prop 发生变化时，其所有后代 consumers 都将重新渲染。

```jsx
// ThemeContext.js
import { createContext } from 'react';
export const ThemeContext = createContext('light');

// App.js
import { ThemeContext } from './ThemeContext';
import Toolbar from './Toolbar';

function App() {
  return (
    // 此 Provider 内的任何组件都可以读取其 value
    <ThemeContext.Provider value="dark">
      <Toolbar />
    </ThemeContext.Provider>
  );
}
```

### 3. 消费 Context

有两种方式可以消费 context 的值。

#### `useContext` Hook (推荐)

在函数组件中，读取 context 的现代且首选方法是使用 `useContext` Hook。它比 `Context.Consumer` 组件更简洁、更易于阅读。

你可以在 [Hooks 文档](./hooks-other.md)中了解更多相关信息。

```jsx
// ThemedButton.js
import { useContext } from 'react';
import { ThemeContext } from './ThemeContext';

function ThemedButton() {
  const theme = useContext(ThemeContext); // 'theme' 将是 'dark'
  return <button className={theme}>我是一个 {theme} 按钮</button>;
}
```

#### `Context.Consumer`

`Context.Consumer` 是一个订阅 context 变化的 React 组件。它需要一个函数作为其子组件（即 render prop）。该函数接收当前的 context `value` 并返回一个 React 节点。

这种方法仍然受支持，但在现代 React 代码库中已不常用。

```jsx
import { ThemeContext } from './ThemeContext';

function ThemedButton() {
  return (
    <ThemeContext.Consumer>
      {theme => (
        <button className={theme}>我是一个 {theme} 按钮</button>
      )}
    </ThemeContext.Consumer>
  );
}
```

## 何时使用 Context

Context 主要用于某些数据需要被不同嵌套层级的多个组件访问的场景。请谨慎使用它，因为它会使组件复用变得更加困难。

如果你只是想避免通过多个层级传递某些 props，组件组合通常是比 context 更简单的解决方案。

适合使用 Context 的场景：
- UI 主题（例如，暗/亮模式）
- 用户认证数据（例如，当前用户对象）
- 应用级别的配置或设置

## 后续步骤

现在你已经对 React Context 如何在组件树中管理类全局状态有了扎实的理解。接下来，你可以探索用于处理组件子元素的工具。

<x-card data-title="Children 工具" data-icon="lucide:list" data-href="/core-apis/children-utilities" data-cta="阅读更多">
  学习如何使用 React.Children API 辅助函数来处理 props.children 这一不透明数据结构。
</x-card>