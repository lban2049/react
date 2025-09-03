# 创建和操作元素

虽然 [JSX](./core-apis-jsx.md) 是在 React 中描述用户界面最常用和最便捷的方式，但理解它只是底层函数调用的语法糖非常重要。其核心在于，每个 JSX 标签都会被转换为一个 `React.createElement()` 调用，该调用会生成一个称为 React 元素的 JavaScript 对象。本节将介绍用于直接创建和操作这些元素的底层 API。

理解这些函数对于构建高阶组件、设计工具库或需要动态地向子元素添加 props 时非常有用。

```d2
direction: down

"JSX": {
  shape: document
  label: "<h1 className='greeting'>Hello</h1>"
}

"Babel Compiler": {
  shape: hexagon
}

"Function Call": {
  shape: rectangle
  label: "React.createElement('h1', {className: 'greeting'}, 'Hello')"
}

"React Element Object": {
  shape: package
  label: "对渲染内容的轻量级描述"
  "$$typeof": "Symbol(react.element)"
  "type": "'h1'"
  "props": "{ className: 'greeting', children: 'Hello' }"
}

"JSX" -> "Babel Compiler": "转译"
"Babel Compiler" -> "Function Call": "输出"
"Function Call" -> "React Element Object": "创建"
```

---

## `createElement()`

`React.createElement()` 是一个基础函数，用于创建并返回一个给定类型的新 React 元素。JSX 转换会编译为使用此函数。

`React.createElement(type, [props], [...children])`

**Parameters**

| Parameter | Type | Description |
|---|---|---|
| `type` | string \| Component | 元素类型。可以是一个标签名称字符串（例如 `'div'`、`'span'`），一个 React 组件类型（类或函数），或一个 React fragment 类型。 |
| `props` | object | 一个包含元素属性 (props) 的对象。此对象不应包含 `key` 等保留 props。如果 `config` 为 `null` 或 `undefined`，则视为空对象。 |
| `children` | ReactNode... | 可变数量的子参数。这些参数可以是其他 React 元素、字符串、数字或节点数组。 |

**Example**

以下 JSX 代码：

```jsx
const element = (
  <h1 className="greeting">
    Hello, world!
  </h1>
);
```

等效于以下 `createElement()` 调用：

```javascript
const element = React.createElement(
  'h1',
  {className: 'greeting'},
  'Hello, world!'
);
```

两个版本都会生成相同的 React 元素对象。

---

## `cloneElement()`

`React.cloneElement()` 以现有 `element` 为起点，克隆并返回一个新的 React 元素。生成的元素将拥有原始元素的 props，并与新的 props 进行浅层合并。

`React.cloneElement(element, [props], [...children])`

**Parameters**

| Parameter | Type | Description |
|---|---|---|
| `element` | ReactElement | 要克隆的 React 元素。必须是有效的 React 元素。 |
| `props` | object | 一个包含新 props 的对象，用于合并到克隆的元素中。除非被覆盖，否则原始元素的 `key` 和 `ref` 将被保留。 |
| `children` | ReactNode... | 将替换现有 `children` 的新子元素。 |

**Usage**

`cloneElement` 主要用于添加或修改传递给组件的子元素的 props。这在需要向其子元素注入 props 的布局组件中很常见。

**Example**

此组件为其所有作为有效 React 元素的直接子元素添加一个 `className` prop。

```javascript
import React from 'react';

function CustomContainer({ children }) {
  const enhancedChildren = React.Children.map(children, child => {
    if (React.isValidElement(child)) {
      // 克隆子元素并添加一个新的 prop
      return React.cloneElement(child, { className: 'enhanced-child' });
    }
    return child;
  });

  return <div className="container">{enhancedChildren}</div>;
}

// 用法：
// <CustomContainer>
//   <p>First paragraph</p>
//   <span>Some text</span>
// </CustomContainer>
// <p> 和 <span> 都将接收 'enhanced-child' 类。
```

---

## `isValidElement()`

`React.isValidElement()` 是一个工具函数，用于验证一个对象是否为 React 元素。

`React.isValidElement(object)`

**Parameters**

| Parameter | Type | Description |
|---|---|---|
| `object` | any | 要检查的对象。 |

**Returns**

如果 `object` 是一个 React 元素，则返回 `true`，否则返回 `false`。

**Example**

此函数通常与 `React.Children.map` 或其他工具函数结合使用，以确保您只尝试操作 React 元素。

```javascript
import React, { isValidElement } from 'react';

const validElement = <div />;
const componentType = () => <div />;
const notAnElement = 'Hello';
const nullValue = null;

console.log(isValidElement(validElement));     // true
console.log(isValidElement(componentType));   // false（它是一个函数，而不是一个元素）
console.log(isValidElement(notAnElement));    // false
console.log(isValidElement(nullValue));         // false
```

---

既然您已经了解了如何创建和操作元素，接下来可以探索如何与传递给组件的子元素进行交互。请在 [Children Utilities](./core-apis-children-utilities.md) 指南中了解更多信息。