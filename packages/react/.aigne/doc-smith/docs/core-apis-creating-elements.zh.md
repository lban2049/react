# 创建和操作元素

虽然 [JSX](./core-apis-jsx.md) 是描述 UI 最常用和最便捷的方式，但它本质上是一组用于创建 React 元素的底层函数的语法糖。理解这些函数可以让你更直接地控制元素的创建，对于某些高级模式和构建工具至关重要。本节介绍了直接操作 React 元素的核心工具。

这方面的主要 API 包括 `React.createElement()`、`React.cloneElement()` 以及辅助函数 `React.isValidElement()`。

## `createElement()`

`createElement()` 函数是 React UI 的原始构建块。在新的 JSX 转换出现之前，每个 JSX 标签都会被编译成一个 `React.createElement()` 调用。

它会创建并返回一个具有指定属性的新的 React 元素对象。该对象是你想渲染内容的一个轻量级描述。

### 语法

```javascript
React.createElement(type, [props], [...children])
```

### 参数

| 参数 | 类型 | 描述 |
|---|---|---|
| `type` | string \| Component | 元素类型。可以是一个标签名称字符串（例如 `'div'`、`'span'`）、一个 React 组件类型（类或函数），或一个 React 片段类型。 |
| `props` | object | 一个包含元素 props 的对象。`key` 是一个特殊 prop，会单独处理。如果没有 props，此参数可以为 `null` 或省略。 |
| `children` | ReactNode | 一个或多个子元素。这些元素在 `props` 之后作为后续参数传入。 |

### 示例

下面是如何使用 `createElement()` 创建一个简单的标题元素，以及其等效的 JSX 写法。

```javascript
import React from 'react';

// 使用 createElement 创建一个 h1 元素
const greeting = React.createElement(
  'h1',
  { className: 'site-title' },
  'Hello, World!'
);

// 上述代码等同于以下 JSX：
// const greeting = <h1 className="site-title">Hello, World!</h1>;
```

当有多个子元素时，可以将它们作为额外的参数传入：

```javascript
const list = React.createElement(
  'ul',
  null, // 没有 props
  React.createElement('li', null, 'Item 1'),
  React.createElement('li', null, 'Item 2')
);

// 等效的 JSX：
// <ul>
//   <li>Item 1</li>
//   <li>Item 2</li>
// </ul>
```

## `cloneElement()`

`cloneElement()` 函数允许你克隆一个现有的 React 元素并向其合并新的 props。这在处理作为 `props.children` 传入的元素时特别有用。

### 语法

```javascript
React.cloneElement(element, [props], [...children])
```

### 参数

| 参数 | 类型 | 描述 |
|---|---|---|
| `element` | ReactElement | 要被克隆的 React 元素。 |
| `props` | object | 一个包含新 props 的对象，这些 props 将被合并到克隆元素的现有 props 中。原始元素的 `key` 和 `ref` 会被保留。 |
| `children` | ReactNode | 新的子元素，它们将完全替换原始元素的子元素。 |

### 示例

假设你有一个组件，需要为传递给它的任何子元素添加一个特定的 `className`。

```javascript
import React from 'react';

function HighlightWrapper({ children }) {
  // 克隆子元素并添加一个新的 className
  const childWithClass = React.cloneElement(children, {
    className: `${children.props.className || ''} highlighted`
  });

  return <div>{childWithClass}</div>;
}

// 用法
function App() {
  return (
    <HighlightWrapper>
      <p className="text-normal">This text will be highlighted.</p>
    </HighlightWrapper>
  );
}
```

在此示例中，`cloneElement` 接收原始的 `<p>` 元素，并返回一个新的 `<p>` 元素，其 `className` prop 中添加了 `highlighted` 类。

## `isValidElement()`

`isValidElement()` 是一个工具函数，用于验证一个变量是否为有效的 React 元素。它会检查对象是否具有 `$$typeof: REACT_ELEMENT_TYPE` 属性。

### 语法

```javascript
React.isValidElement(object)
```

### 参数

| 参数 | 类型 | 描述 |
|---|---|---|
| `object` | any | 要检查的对象。 |

### 返回值

如果 `object` 是一个 React 元素，则返回 `true`，否则返回 `false`。

### 示例

这在辅助函数或组件中非常有用，当你需要确保正在处理的是一个可渲染元素，然后再尝试操作它时。

```javascript
import React, { isValidElement } from 'react';

function renderContent(content) {
  if (isValidElement(content)) {
    // 这是一个 React 元素，所以我们可以直接渲染它
    return content;
  } else if (typeof content === 'string') {
    // 这是一个字符串，用 p 标签包裹它
    return <p>{content}</p>;
  } else {
    // 这是其他类型，返回 null 或一个默认值
    return null;
  }
}

console.log(isValidElement(<div />)); // true
console.log(isValidElement('Hello')); // false
console.log(isValidElement({})); // false
```

## 幕后原理：现代 JSX 运行时

虽然 `createElement` 是基础，但现代 React 项目通常使用一种新的 JSX 转换。新的转换不会将 JSX 编译为 `React.createElement`，而是使用来自一个特殊的 `react/jsx-runtime` 入口点的 `jsx` 和 `jsxs` 等函数。这种优化避免了在每个使用 JSX 的文件中都导入 `React` 的需要。

即使使用了这种新的转换，输出的仍然是一个 React 元素对象。核心原则保持不变：你的代码描述 UI，React 创建元素对象来表示它。这里讨论的函数，特别是 `cloneElement` 和 `isValidElement`，对于直接操作元素仍然具有现实意义，无论使用哪种 JSX 转换。

---

现在你已经了解了如何在底层操作元素，合乎逻辑的下一步是学习如何与它们渲染的实际 DOM 节点进行交互。请继续阅读 [Refs](./core-apis-refs.md) 部分以了解更多信息。