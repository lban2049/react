# JSX

JSX 是 JavaScript 的语法扩展，它允许你使用类似 HTML 的语法来编写 UI 结构。虽然它可能让你联想到模板语言，但它具备 JavaScript 的全部功能。JSX 为定义 React 元素提供了一种简洁而熟悉的方式。

如需深入了解其设计和规范，请参阅原始提案：[RFC: New JSX Transform](https://github.com/reactjs/rfcs/pull/107)。

## JSX 转换

浏览器本身无法直接理解 JSX，需要通过 Babel 或 TypeScript 等工具将其编译为标准 JavaScript。现代 JSX 转换会将 JSX 转换为对特殊函数的调用，而无需在每个文件的作用域内导入 `React`。

例如，以下 JSX 代码：

```jsx
const element = (
  <div className="greeting">
    <h1>Hello, World!</h1>
  </div>
);
```

会通过 `jsxs` 函数编译成以下 JavaScript 对象：

```javascript
// Compiled output
import {jsxs as _jsxs} from 'react/jsx-runtime';
import {jsx as _jsx} from 'react/jsx-runtime';

const element = _jsxs('div', {
  className: 'greeting',
  children: [
    _jsx('h1', { children: 'Hello, World!' })
  ]
});
```

React 正是使用这个编译后的输出来创建实际的 DOM 元素并管理 UI。

## JSX 运行时

React 提供了针对特定环境和用途定制的不同 JSX 运行时。你的构建工具会自动选择合适的运行时，但了解它们之间的区别会很有帮助。

| Runtime Package                  | Purpose                                                              |
|----------------------------------|----------------------------------------------------------------------|
| `react/jsx-runtime`              | 用于客户端环境的生产运行时。                 |
| `react/jsx-dev-runtime`          | 用于客户端环境的开发运行时。                |
| `react/jsx-runtime.react-server` | 用于服务器环境（如 RSC）的生产运行时。           |
| `react/jsx-dev-runtime.react-server` | 用于服务器环境的开发运行时。                     |

### 生产运行时与开发运行时

主要区别在于所提供的验证和调试信息的级别。

- **生产环境 (`jsx`, `jsxs`)**: 这些函数针对性能进行了优化。它们创建 `ReactElement` 对象时只包含所需的最小属性：`$$typeof`、`type`、`key`、`ref` 和 `props`。

- **开发环境 (`jsxDEV`)**: 此函数包含大量的验证和警告，以帮助你在开发过程中发现潜在问题。例如，`jsxDEV` 会：
  - 验证数组中的子元素是否具有唯一的 `key` prop。
  - 如果你从另一个对象展开 `key` prop，它会发出警告，因为 key 必须直接传递。
  - 将 `_owner`、`_debugStack` 和 `_debugTask` 等调试信息附加到元素对象上，以改善错误信息和开发者工具的集成。

## 核心 JSX 函数

JSX 转换使用几个关键函数来创建元素。

### `jsx` 与 `jsxs`

编译器会自动在 `jsx` 和 `jsxs` 之间进行选择以进行优化：
- `jsx`: 用于具有单个子元素或动态生成的子元素（例如，通过数组的 map 方法生成）的元素。
- `jsxs`: 用于具有多个静态子元素的元素。这使得 React 可以在已知子元素是静态数组的情况下执行优化。

```jsx
// Compiles to a call to jsx()
const singleChild = <div>{name}</div>;

// Compiles to a call to jsxs()
const multipleChildren = (
  <div>
    <p>First item</p>
    <p>Second item</p>
  </div>
);
```

### `Fragment`

React Fragments 允许你将一组子元素组合在一起，而无需向 DOM 添加额外的节点。这在从组件返回多个元素时非常有用。

JSX 为 Fragments 提供了简写语法 (`<>...</>`)：

```jsx
function UserInfo() {
  return (
    <>
      <h2>User Name</h2>
      <p>User Description</p>
    </>
  );
}

// The above is equivalent to:
import { Fragment } from 'react/jsx-runtime';

function UserInfo() {
  return (
    <Fragment>
      <h2>User Name</h2>
      <p>User Description</p>
    </Fragment>
  );
}
```

## 特殊 Prop 处理：`key`

The `key` prop 很特殊。React 使用它作为提示，来识别元素列表中的哪些项发生了变化、被添加或被移除。

**重要提示**：`key` 不会作为 prop 传递给你的组件。如果你需要在组件内部访问 `key` 的值，必须将其作为另一个不同的 prop 来传递。

在开发过程中，如果你尝试访问 `this.props.key`，React 会发出警告。

```javascript
// In development, this function is defined to warn about accessing `key`.
function defineKeyPropWarningGetter(props, displayName) {
  const warnAboutAccessingKey = function () {
    // ... (warning logic)
    console.error(
      '%s: `key` is not a prop. Trying to access it will result ' +
      'in `undefined` being returned. If you need to access the same ' +
      'value within the child component, you should pass it as a different ' +
      'prop. (https://react.dev/link/special-props)',
      displayName,
    );
  };
  // ... (Object.defineProperty logic)
}
```

---

现在你已经了解了 JSX 的底层工作原理，接下来请在 [组件与 Props](./core-apis-components-and-props.md) 中学习如何将其与 JavaScript 逻辑相结合，以构建可复用的 UI 元素。