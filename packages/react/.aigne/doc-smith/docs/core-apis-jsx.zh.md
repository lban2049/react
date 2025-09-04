# JSX

JSX 是 JavaScript 的一个语法扩展，允许你用一种熟悉的、类似 HTML 的语法来编写 UI 结构。它是编写 React 组件的基础部分，提供了一种简洁易读的方式来描述 UI 的外观。虽然它可能看起来像一种模板语言，但 JSX 拥有 JavaScript 的全部功能。

本节将探讨 JSX 如何转换为浏览器可以理解的原生 JavaScript，并详细介绍 React 为客户端和服务器环境提供的不同运行时。

## JSX 转换

浏览器本身无法直接理解 JSX。你的代码需要通过编译器（如 Babel 或 TypeScript）运行，将 JSX 语法转换为标准的 JavaScript 函数调用。

过去，这种转换会将 JSX 转换为 `React.createElement(...)` 调用。然而，现代 JSX 转换会自动将 JSX 转换为特殊的 `jsx(...)` 函数调用，这可以提升性能、简化代码，并且无需为了使用 JSX 而在每个文件中导入 `React`。

以下是该转换过程的概念性概述：

```d2
direction: down

"jsx-code": {
  label: "你的 JSX 代码\n<MyComponent name=\"React\" />"
  shape: code
}

compiler: {
  label: "编译器\n（Babel、TypeScript 等）"
  shape: rectangle
}

"runtime-functions": {
  label: "React 运行时函数"
  shape: package
  grid-columns: 2

  "prod-runtime": {
    label: "生产环境运行时"
    shape: rectangle
    "jsx()": {}
    "jsxs()": {}
  }

  "dev-runtime": {
    label: "开发环境运行时"
    shape: rectangle
    "jsxDEV()": {}
  }
}

"jsx-code" -> compiler: "1. 编译"
compiler -> "runtime-functions": "2. 输出函数调用"

```

## 特定于环境的运行时

React 提供了针对客户端和服务器环境以及生产和开发模式的不同运行时包。这种分离设计使得针对特定环境的优化和功能成为可能。

通常，你会配置构建工具来自动使用正确的运行时。主要入口点如下：

| 入口点 | 环境 | 描述 |
|---|---|---|
| `react/jsx-runtime` | 客户端（生产环境） | 用于浏览器的标准优化运行时。 |
| `react/jsx-dev-runtime` | 客户端（开发环境） | 用于开发的特殊运行时，包含额外的验证和警告。 |
| `react/jsx-runtime.react-server` | 服务器（生产环境） | 专为服务器环境（如 React Server Components）优化的运行时。 |
| `react/jsx-dev-runtime.react-server`| 服务器（开发环境） | 服务器运行时的开发版本，增加了调试功能。 |

## 核心运行时函数

现代 JSX 转换使用由运行时导出的几个关键函数：

*   `jsx(type, config, maybeKey)`: 这是用于创建 React 元素的主要函数。它在元素只有一个子元素或子元素是动态传递时被调用。

*   `jsxs(type, config, maybeKey)`: `jsx` 的一个变体，当元素有多个静态子元素（即子元素在源代码中被定义为数组）时用作优化。这使得 React 可以执行潜在的优化。

*   `jsxDEV(type, config, maybeKey, isStaticChildren)`: 这是该函数的仅限开发版本。它对提升开发者体验至关重要，因为它包含了大量的验证和警告逻辑。例如，它会警告列表中缺少 `key` prop、`key` prop 的滥用，并在开发过程中提供更详细的错误消息和组件堆栈跟踪。

## 重要注意事项

在使用 JSX 时，需要注意几个特殊的 prop 和概念。

### `key` Prop

`key` prop 是一个特殊的字符串属性，在创建元素列表时需要包含它。Key 可以帮助 React 识别哪些项发生了变化、被添加或被删除。在一个兄弟元素列表中，Key 应该是稳定、可预测且唯一的。

```javascript
const todoItems = todos.map((todo) =>
  <li key={todo.id}>
    {todo.text}
  </li>
);
```

在开发模式下，如果你忘记为数组中的项提供 `key`，React 会发出警告。请注意，`key` 不是一个标准的 prop，不能从子组件中访问（例如，通过 `props.key`）。

### Fragments

在 React 中，一个常见的模式是让一个组件返回多个元素。JSX 要求有单一的根元素。Fragments 允许你将一组子元素分组，而无需向 DOM 添加额外的节点。

你可以使用显式导出的 `Fragment`，也可以使用更简洁的 `<>...</>` 语法。

```javascript
import { Fragment } from 'react';

function Columns() {
  return (
    <>
      <td>Hello</td>
      <td>World</td>
    </>
  );
}
```

### `ref` Prop

从 React 19 开始，`ref` 是一个常规 prop，你可以将其传递给组件以获取对 DOM 元素或类组件实例的引用。之前，访问 `element.ref` 是可行的，但现在已被弃用。你应该始终通过 `ref` prop 与 ref 进行交互。

---

理解 JSX 转换及其运行时有助于深入了解 React 的内部工作原理。对于需要以编程方式创建元素而不使用 JSX 的情况，你可以在 [创建和操作元素](./core-apis-creating-elements.md) 指南中了解更多信息。