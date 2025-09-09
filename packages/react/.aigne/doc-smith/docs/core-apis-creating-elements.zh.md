# 创建和操作元素

虽然你通常会使用 [JSX](./core-apis-jsx.md) 来定义用户界面，但理解 JSX 是用于创建和管理 React 元素的一组底层函数的语法糖，这一点非常重要。

这些函数提供了一种与 React 核心渲染机制直接交互的方式，对于构建可复用组件和高级模式至关重要。

React 元素是一个轻量、无状态、不可变的对象，用于描述你希望在屏幕上看到的内容。它是一个纯粹的 JavaScript 对象，而不是组件实例或 DOM 节点。React 读取这些对象，并用它们来构建 DOM 并保持其更新。

本指南涵盖了直接创建和操作这些元素对象的主要 API。

## `createElement()`

`React.createElement()` 是 JSX 编译所依赖的基础构建模块。它会根据给定的类型创建并返回一个新的 React 元素。

### 语法

```javascript React.createElement() icon=logos:react
React.createElement(type, [props], [...children])
```

### 参数

| Parameter | Type | Description |
|---|---|---|
| `type` | `string \| function \| class` | 元素类型。可以是一个标签名称字符串（例如 `'div'`）、一个 React 组件类型（函数或类）或一个 React 片段类型。 |
| `props` | `object` | 一个包含元素 props 的对象。如果需要，应在此处包含 `key` 这一特殊属性。 |
| `...children` | `ReactNode` | 元素的子元素。你可以将多个子元素作为后续参数传递，它们将在 `props.children` 中可用。 |

### 示例

以下示例展示了如何创建一个带有子 `span` 元素的简单标题元素。

```javascript icon=logos:react
import React from 'react';

const greetingElement = React.createElement(
  'h1',
  { className: 'greeting' },
  'Hello, ',
  React.createElement('span', { style: { color: 'blue' } }, 'world!')
);

// 这等同于以下 JSX：
// <h1 className="greeting">Hello, <span style={{ color: 'blue' }}>world!</span></h1>
```

## `cloneElement()`

`React.cloneElement()` 以一个现有元素为起点，克隆并返回一个新的 React 元素。生成的元素将拥有原始元素的 props，并浅层合并新的 props。新的子元素将替换现有的子元素。

这对于添加或修改传递给你组件的元素的 props（例如 `props.children`）特别有用。

### 语法

```javascript React.cloneElement() icon=logos:react
React.cloneElement(element, [props], [...children])
```

### 参数

| Parameter | Type | Description |
|---|---|---|
| `element` | `ReactElement` | 要克隆的 React 元素。 |
| `props` | `object` | 要合并到克隆元素现有 props 中的新 props。原始元素的 `key` 和 `ref` 将被保留。 |
| `...children` | `ReactNode` | 将替换原始元素 `children` prop 的新子元素。 |

### 示例

此示例展示了一个组件，它包裹其子元素并添加一个新的 CSS 类。

```javascript icon=logos:react
import React from 'react';

function AddWrapper({ children }) {
  // React.Children.only 确保 children 只有一个子元素。
  const child = React.Children.only(children);
  
  // 克隆子元素并添加一个新的类名
  const childWithClass = React.cloneElement(child, {
    className: `${child.props.className || ''} special-wrapper`
  });

  return <div>{childWithClass}</div>;
}

// 当像这样使用时：
// <AddWrapper>
//   <p className="original">Some text</p>
// </AddWrapper>

// 它将渲染：
// <div>
//   <p class="original special-wrapper">Some text</p>
// </div>
```

## `isValidElement()`

`React.isValidElement()` 是一个工具函数，用于验证一个对象是否为 React 元素。如果对象是有效元素，则返回 `true`，否则返回 `false`。

### 语法

```javascript React.isValidElement() icon=logos:react
React.isValidElement(object)
```

### 参数

| Parameter | Type | Description |
|---|---|---|
| `object` | `any` | 要检查的对象。 |

### 示例

这对于安全地渲染可能是也可能不是 React 元素的 props 非常有用。

```javascript icon=logos:react
import React from 'react';

function Card({ header, content }) {
  return (
    <div className="card">
      <div className="card-header">
        {React.isValidElement(header) ? header : <h2>{header}</h2>}
      </div>
      <div className="card-content">
        {content}
      </div>
    </div>
  );
}

// 用法：
// <Card header={<h1>Custom Header Element</h1>} content="..." />
// <Card header="Simple Header String" content="..." />
```

## 后续步骤

理解这些核心函数可以让你更深入地了解 React 的内部工作原理。掌握了这些知识，你就能更好地构建复杂而灵活的组件。

<x-cards>
  <x-card data-title="Refs" data-icon="lucide:anchor" data-href="/core-apis/refs">
    学习如何访问 React 元素渲染成的 DOM 节点并与之交互。
  </x-card>
  <x-card data-title="Children Utilities" data-icon="lucide:list-tree" data-href="/core-apis/children-utilities">
    探索用于处理 props.children 数据结构的 React.Children API，这通常涉及使用 cloneElement。
  </x-card>
</x-cards>