# JSX

JSX 是 JavaScript 的一种语法扩展，它允许你直接在 JavaScript 代码中编写类似于 HTML 的 UI 结构。它是 React 的一个关键特性，使组件渲染具有声明性且易于阅读。虽然它看起来像一种模板语言，但 JSX 完全由 JavaScript 驱动，并被编译成创建 React 元素的常规 JavaScript 函数调用。

要深入了解 JSX 生成的元素，请参阅 [创建和操作元素](./core-apis-creating-elements.md) 指南。

## JSX 转换

现代 React 工具链使用一种新的 JSX 转换，它会自动将 JSX 代码转换为函数调用，而无需你在每个文件中导入 `React`。这简化了组件的编写。

例如，这段 JSX 代码：

```javascript A Simple JSX Component icon=logos:react
const Greeting = () => {
  return <h1>Hello, World!</h1>;
};
```

经编译器转换后如下：

```javascript Compiled Output icon=logos:javascript
import { jsx as _jsx } from 'react/jsx-runtime';

const Greeting = () => {
  return _jsx('h1', { children: 'Hello, World!' });
};
```

注意 `_jsx` 是如何从 `react/jsx-runtime` 导入的。这种自动导入由你的构建工具（如 Babel 或 SWC）处理，具体从哪个文件导入取决于环境。

## JSX 运行时

React 提供了针对特定环境的不同 JSX 运行时入口点。你的构建配置会自动选择正确的入口点。这种分离确保了开发版本包含有用的警告和调试工具，而生产版本则为性能进行了优化。

| 入口点 | 环境 | 用途 |
|-----------------------------------|------------------------|---------------------------------------------------------------|
| `react/jsx-runtime` | Production (Client) | 用于客户端应用程序的标准、优化后的运行时。 |
| `react/jsx-dev-runtime` | Development (Client) | 包含额外验证和警告的客户端运行时。 |
| `react/jsx-runtime.react-server` | Production (Server) | 为 RSC 等纯服务器环境优化的运行时。 |
| `react/jsx-dev-runtime.react-server`| Development (Server) | 带有警告的服务器运行时开发版本。 |

这些不同的运行时允许 React 提供特定于环境的功能，而不会增加生产包的体积。要了解更多关于架构差异的信息，请参阅我们的高级指南 [服务器与客户端环境](./advanced-server-vs-client.md)。

## 核心 JSX 函数

JSX 转换主要使用三个函数来创建元素：

<x-cards data-columns="3">
  <x-card data-title="jsx()" data-icon="lucide:box">
    用于具有单个子元素或动态生成子元素（例如，通过数组映射生成）的元素。
  </x-card>
  <x-card data-title="jsxs()" data-icon="lucide:boxes">
    针对具有多个静态子元素的元素的优化。编译器将它们作为静态数组传递，从而可能提高性能。
  </x-card>
  <x-card data-title="jsxDEV()" data-icon="lucide:wrench">
    仅用于开发环境的版本。它包含广泛的验证，例如检查列表中的 key 是否唯一，以及对不当的 prop 用法发出警告。
  </x-card>
</x-cards>

`jsxDEV` 函数在开发过程中特别有用。例如，它会验证数组中的子元素是否具有唯一的 `key` prop，并且如果你通过 props 对象展开 `key` 而不是直接传递它，它会发出警告。

```javascript jsxDEV Warning Example icon=logos:react
// 在开发环境中，此代码将产生警告，因为 key
// 是从 props 对象中展开的。
const props = { key: 'unique-id', text: 'Hello' };
const element = <div {...props} />; 

// 正确用法：
const elementWithKey = <div key={props.key} {...props} />;
```

## 特殊 Props：key 和 ref

`key` 和 `ref` 这两个 prop 由 React 保留，并具有特殊含义。它们分别用于 React 的协调算法和 DOM 访问。它们不会作为 `props` 对象的一部分传递给组件。

- **`key`**：列表中元素的稳定标识符。它帮助 React 识别哪些项已更改、添加或删除。
- **`ref`**：提供一种访问在 render 方法中创建的 DOM 节点或 React 组件的方式。

在开发模式下，如果你尝试从子组件内部访问 `props.key`，React 会发出警告，引导你在需要时通过不同的 prop 传递该值。

---

现在你已经了解 JSX 语法如何转换为函数调用，可以探索这些函数创建的对象了。继续阅读 [创建和操作元素](./core-apis-creating-elements.md) 指南，以了解有关 `ReactElement` 的更多信息。