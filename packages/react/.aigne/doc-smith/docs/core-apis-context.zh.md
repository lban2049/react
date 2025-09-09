# Context

在典型的 React 应用中，数据通过 props 自上而下地从父组件传递到子组件。然而，对于需要在不同嵌套级别的许多组件中访问的某些类型的 props，这种方式可能会变得冗长和不便。这通常被称为“prop 逐层传递”。

Context 提供了一种无需在每个层级手动传递 props 即可在组件树中传递数据的方法。它旨在共享可被视为 React 组件树的“全局”数据，例如当前已认证的用户、主题或首选语言。

## 创建 Context

第一步是使用 `createContext` 函数创建一个 Context 对象。该函数接受一个可选的默认值，当消费者在其上方的组件树中没有匹配的 Provider 时，会使用该默认值。这对于在不包裹组件的情况下单独测试组件非常有用。

```javascript Creating a Context Object icon=logos:react
import { createContext } from 'react';

// 默认值为 'light'
export const ThemeContext = createContext('light');
```

`createContext` 函数返回一个 context 对象，该对象包含两个组件：`Provider` 和 `Consumer`。

## 使用 `Context.Provider` 提供 Context

要使 context 的值对后代组件可用，你需要使用 context 的 `Provider` 组件包裹部分组件树。`Provider` 接受一个 `value` prop，该 prop 的值将被传递给此 Provider 的所有后代消费组件。

```jsx App.js: Using the Provider icon=logos:react
import { ThemeContext } from './ThemeContext';
import Toolbar from './Toolbar';

function App() {
  // 值 'dark' 将被向下传递
  return (
    <ThemeContext.Provider value="dark">
      <Toolbar />
    </ThemeContext.Provider>
  );
}
```

`ThemeContext.Provider` 内部的任何组件现在都可以读取 'dark' 这个值。组件获取的 context 值由其在组件树中离它最近的 `Provider` 的 `value` prop 决定。

## 消费 Context

消费 context 值主要有两种方式：`useContext` Hook 和 `Context.Consumer` 组件。

### `useContext` Hook (推荐)

The `useContext` Hook 是在函数组件中读取 context 值的现代且最简单的方法。它接受 context 对象本身（即 `createContext` 的返回值）并返回当前的 context 值。

```jsx ThemedButton.js: Using the useContext Hook icon=logos:react
import { useContext } from 'react';
import { ThemeContext } from './ThemeContext';

function ThemedButton() {
  const theme = useContext(ThemeContext);
  return <button className={`theme-${theme}`}>Click Me</button>;
}
```

这个 Hook 使你的组件更清晰、更易读。更多详情，请参阅 [Hooks 文档](./hooks-other.md)。

### `Context.Consumer` 组件

对于类组件或较旧的代码库，你可以使用 `Context.Consumer` 组件。该组件使用“render prop”模式，需要一个函数作为其子节点。该函数接收当前的 context 值并返回一个 React 元素。

```jsx ThemedButton.js: Using the Consumer Component icon=logos:react
import { ThemeContext } from './ThemeContext';

function ThemedButton() {
  return (
    <ThemeContext.Consumer>
      {theme => (
        <button className={`theme-${theme}`}>Click Me</button>
      )}
    </ThemeContext.Consumer>
  );
}
```

## 何时使用 Context

Context 是管理需要被许多组件访问的状态的绝佳工具，例如：

*   **主题化**：传递主题信息（例如颜色、字体）。
*   **用户认证**：共享当前用户的数据和认证状态。
*   **应用级设置**：管理区域设置或用户偏好。

然而，重要的是不要过度使用它。如果你只是将数据向下一两层传递，组件组合通常是比 context 更简单、更易于维护的解决方案。

## 后续步骤

既然你已经了解了如何使用 Context 管理全局数据，你可以探索其他 React 核心 API 或深入学习 Hooks。

<x-cards>
  <x-card data-title="Refs" data-icon="lucide:mouse-pointer-square" data-href="/core-apis/refs">
    了解如何直接访问 DOM 节点或 React 组件并与之交互。
  </x-card>
  <x-card data-title="Hooks" data-icon="lucide:anchor" data-href="/hooks">
    探索所有内置的 Hooks，包括对 useContext 的更深入介绍。
  </x-card>
</x-cards>