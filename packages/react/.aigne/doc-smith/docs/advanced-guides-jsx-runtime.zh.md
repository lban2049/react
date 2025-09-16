# JSX 运行时

现代 React JSX 转换会自动将 JSX 代码转换为常规的 JavaScript 函数调用，而无需将 `React` 引入作用域。本指南详细介绍了它所使用的函数及其底层工作原理。

传统上，JSX 会被编译为 `React.createElement(...)` 调用。然而，新的转换将 JSX 编译为 `jsx(...)` 函数调用，这可以带来性能优化并简化文件结构。要了解更多背景信息，可以阅读关于[新的 JSX 转换](https://react.dev/link/new-jsx-transform)的文档。

## 核心运行时导出

JSX 运行时提供了一些由编译器使用的关键函数。通常你不需要直接导入或使用它们，但了解它们对于高级用例或调试很有帮助。

| Function | Package | Description |
|---|---|---|
| `jsx` | `react/jsx-runtime` | 创建一个 React 元素。用于具有单个子元素或动态子元素的元素。 |
| `jsxs` | `react/jsx-runtime` | `jsx` 的优化版本，用于具有多个静态子元素的元素（即子元素作为数组传递）。 |
| `jsxDEV` | `react/jsx-dev-runtime` | `jsx` 的仅开发版本，包含额外的验证、警告（如列表中缺少 `key` 属性）和调试信息。 |
| `Fragment` | `react/jsx-runtime` | 一个组件，可以让你将一组子元素分组，而无需向 DOM 添加额外的节点。对应于 `<>...</>` 语法。 |

## 工作原理

带有新 JSX 转换的构建工具（如现代版本的 Babel 或 TypeScript）将处理你的 JSX 源代码并输出标准的 JavaScript。

### 单个元素示例

对于一个简单的元素，编译器使用 `jsx` 函数。

```jsx JSX 源代码 icon=logos:react
const element = <h1 className="greeting">Hello, world!</h1>;
```

此代码被编译为：

```javascript 编译后输出 icon=logos:javascript
import { jsx } from 'react/jsx-runtime';

const element = jsx('h1', { className: 'greeting', children: 'Hello, world!' });
```

### 多个子元素示例

对于具有多个静态子元素的元素，编译器可能会使用 `jsxs` 函数作为优化。

```jsx JSX 源代码 icon=logos:react
const element = (
  <div>
    <span>Hello</span>
    <span>World</span>
  </div>
);
```

此代码被编译为：

```javascript 编译后输出 icon=logos:javascript
import { jsxs } from 'react/jsx-runtime';
import { jsx } from 'react/jsx-runtime';

const element = jsxs('div', {
  children: [
    jsx('span', { children: 'Hello' }),
    jsx('span', { children: 'World' })
  ]
});
```

## 特定于环境的运行时

React 提供了两个不同的 JSX 运行时包以支持不同的环境：

- **`react/jsx-runtime`**：这是生产环境运行时。它为性能进行了优化，不包含开发版本中的警告和调试辅助功能。`jsx` 和 `jsxs` 都映射到轻量级的 `jsxProd` 实现。

- **`react/jsx-dev-runtime`**：这是开发环境运行时。它在开发期间用于提供有用的警告和更详细的错误信息。例如，如果你忘记为数组中的元素添加 `key`，它会发出警告。此运行时导出 `jsxDEV`，这是一个更强大的函数，用于收集调试信息。

你的构建设置应根据 `NODE_ENV` 环境变量自动选择正确的运行时。

## 函数签名

JSX 运行时的核心函数具有以下签名。

### `jsx(type, config, maybeKey)`

这是在生产环境中创建元素的主要函数。

<x-field data-name="type" data-type="string | Function | class" data-required="true" data-desc="元素类型。对于像 'div' 这样的内置元素，这是一个字符串。对于自定义组件，这是一个函数或类。"></x-field>
<x-field data-name="config" data-type="object" data-required="true" data-desc="一个包含传递给元素的所有 props 的对象。'children' prop（如果存在）也包含在此对象中。"></x-field>
<x-field data-name="maybeKey" data-type="string | number" data-required="false" data-desc="一个可选的 key，如果在 JSX 中明确提供了 key，则作为第三个参数传递。如果 key 被展开到 props 中（例如，{...{key: '123'}}），它将从 'config' 对象中提取。"></x-field>


### `jsxDEV(type, config, maybeKey, isStaticChildren)`

这是仅用于开发的版本，用于创建元素并提供额外的验证。

<x-field data-name="type" data-type="string | Function | class" data-required="true" data-desc="元素类型，与 jsx 中的相同。"></x-field>
<x-field data-name="config" data-type="object" data-required="true" data-desc="props 对象，与 jsx 中的相同。"></x-field>
<x-field data-name="maybeKey" data-type="string | number" data-required="false" data-desc="可选的 key，与 jsx 中的相同。"></x-field>
<x-field data-name="isStaticChildren" data-type="boolean" data-required="true" data-desc="一个由编译器传递的布尔标志，指示子元素是否为静态数组。这有助于 React 执行优化和验证。"></x-field>


## 与 `createElement` 的比较

现代 JSX 运行时取代了经典的 `React.createElement` 转换。主要区别如下：

1.  作用域：`React.createElement` 要求每个使用 JSX 的文件都必须导入 `React`。而新的运行时则不需要。
2.  参数：`createElement` 在 `props` 对象之后将子元素作为可变数量的参数接收。而 `jsx` 函数则期望 `children` 是 `props` (`config`) 对象内的一个属性。
3.  性能：通过将 props 作为单个对象传递，并使用专门的 `jsxs` 和 `jsxDEV` 函数，新的运行时可以更高效，并提供更好的开发时反馈。

虽然 `createElement` 仍然是 React 顶级 API 的一部分，以实现向后兼容和手动创建元素，但 JSX 转换默认不再编译为它。

---

了解 JSX 运行时有助于深入理解 React 的基本工作原理。对于更高级的主题，你可能对实验性的 [React 编译器运行时](./advanced-guides-compiler-runtime.md) 感兴趣。