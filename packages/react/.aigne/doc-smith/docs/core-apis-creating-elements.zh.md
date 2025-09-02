# 创建和操作元素

虽然 [JSX](./core-apis-jsx.md) 是描述 UI 最常见的方式，但它只是底层 React 函数的一种便捷语法。理解这些函数可以让你更直接地控制 React 元素，并且对于某些高级模式很有用。React 元素是描述你希望在屏幕上看到的内容的轻量级对象。

本指南涵盖了直接操作它们的主要 API：`createElement`、`cloneElement` 和 `isValidElement`。

## createElement()

这是创建 React 元素的基本方法。在 JSX 出现之前，所有的 React UI 都是用 `createElement` 编写的。当使用经典的 JSX 转换时，你的 JSX 代码会直接编译成 `React.createElement` 调用。

**语法**

```javascript
React.createElement(type, [props], [...children])
```

**参数**

| Parameter | Type | Description |
|---|---|---|
| `type` | `string` \| `Component` | 元素类型。可以是一个标签名称字符串（例如，`'div'`）或一个 React 组件（一个类或一个函数）。 |
| `props` | `object` | 包含元素 props 的对象。`null` 被视为空对象。`key` 是一个特殊的保留 prop。 |
| `children` | `ReactNode` | 零个或多个子元素。可以是其他 React 元素、字符串、数字等。 |

**示例**

```javascript
import React from 'react';

// 使用 JSX，它看起来像这样：
const jsxElement = <h1 className="greeting">Hello, world!</h1>;

// 使用 React.createElement，它等效于此：
const createElementElement = React.createElement(
  'h1',
  { className: 'greeting' },
  'Hello, world!'
);

// 你也可以嵌套元素：
const nestedElement = React.createElement(
  'div',
  null, // 无 props
  React.createElement('p', null, 'This is a paragraph inside a div.')
);
```

这表明 `createElement` 是 JSX 这种便利性语法所依赖的、虽然冗长但明确的基础。

## cloneElement()

此实用工具允许你以另一个元素为起点创建一个新的 React 元素。它在修改元素的 props 时特别有用，尤其是修改作为 `props.children` 传入的元素。

**语法**

```javascript
React.cloneElement(element, [props], [...children])
```

**参数**

| Parameter | Type | Description |
|---|---|---|
| `element` | `ReactElement` | 要克隆的元素。这必须是一个有效的 React 元素。 |
| `props` | `object` | 新的 props 将浅层合并到原始元素的 props 中。此对象中的 `key` 和 `ref` 具有优先权。 |
| `children` | `ReactNode` | 新的子元素，将完全替换原始元素的子元素。 |

**示例**

```javascript
import React from 'react';

// 一个为其直接子元素添加 CSS 类的组件
function Tabbable({ children }) {
  // 我们只能克隆单个元素。
  const element = React.Children.only(children);

  if (React.isValidElement(element)) {
    return React.cloneElement(element, {
      className: `${element.props.className || ''} tabbable-item`,
      tabIndex: 0 // 使其可聚焦
    });
  }
  return children;
}

// 用法：
function App() {
  return (
    <Tabbable>
      <button onClick={() => alert('Clicked!')}>Click Me</button>
    </Tabbable>
  );
}
```

在此示例中，`Tabbable` 组件接收一个子元素，克隆它，并合并新的 `className` 和 `tabIndex` props 来增强其行为，而子组件无需知晓这一点。

## isValidElement()

此函数验证给定对象是否为 React 元素。它提供了一种安全的方法，用于检查是否可以使用 `cloneElement` 等元素特定逻辑来处理某个对象。

**语法**

```javascript
React.isValidElement(object)
```

**参数**

| Parameter | Type | Description |
|---|---|---|
| `object` | `any` | 要检查的对象。 |

如果 `object` 是一个 React 元素，则返回 `true`，否则返回 `false`。其内部原理是检查 `$$typeof: REACT_ELEMENT_TYPE` 属性是否存在。

**示例**

```javascript
import React from 'react';

function Wrapper({ children }) {
  const validElementCount = React.Children.toArray(children).filter(React.isValidElement).length;

  return (
    <div>
      <p>Number of valid React Elements passed as children: {validElementCount}</p>
      <div className="content">
        {children}
      </div>
    </div>
  );
}

// 用法：
<Wrapper>
  <h1>A Title</h1>
  {'Just a string'}
  {null}
  <p>A paragraph.</p>
</Wrapper>
// 渲染结果：
// Number of valid React Elements passed as children: 2
```

此示例使用 `isValidElement` 来计算 children 中有多少是实际的 React 元素，而不是像字符串或 `null` 这样的其他原始类型。

## 总结

虽然你在日常开发中主要使用 JSX，但理解 `createElement`、`cloneElement` 和 `isValidElement` 对于构建灵活的组件和库非常有价值。这些函数提供了操作作为 props 传递的元素所需的底层控制，从而实现了强大的模式和抽象。

既然你已经了解了如何创建元素，接下来可以通过探索 [Refs](./core-apis-refs.md) 来学习如何与它们渲染出的底层 DOM 节点进行交互。
