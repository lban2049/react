# Children 工具函数

在 React 中，`props.children` 允许对组件进行组合，但它是一个不透明的数据结构。它可以是单个 JSX 元素、元素数组、字符串、数字，甚至可以是 `undefined`。如果该值不是一个数组，直接使用像 `map` 这样的标准 JavaScript 方法来操作 `props.children` 可能会导致错误。

`React.Children` 对象提供了一组专门设计的工具函数，用于安全高效地处理这种不透明的结构。这些辅助函数可以正确处理你传入的任何类型的 children，确保你的组件健壮且可预测。

```d2
direction: down

"props.children": {
  shape: package
  label: "不透明的数据结构"
  grid-columns: 2

  "单个元素": { shape: rectangle }
  "元素数组": { shape: rectangle }
  "字符串或数字": { shape: rectangle }
  "null 或 undefined": { shape: rectangle }
}

"React.Children 工具函数": {
  shape: rectangle
  style: {
    stroke: "#0052cc"
  }
  grid-columns: 3

  "map": { shape: class }
  "forEach": { shape: class }
  "count": { shape: class }
  "toArray": { shape: class }
  "only": { shape: class }
}

"Transformed Children": {
  shape: package
  label: " 可预测的输出"
  style.fill: "#f6ffed"

  "带有稳定 key 的克隆元素": { shape: rectangle }
}

"props.children" -> "React.Children 工具函数": "传入处理"
"React.Children 工具函数" -> "Transformed Children": "返回"
```

---

## `React.Children.map`

对 `children` 中包含的每个直接子元素调用一个函数，并返回一个包含结果的新数组。它类似于 `Array.prototype.map()`，但它能处理 `children` 是单个元素或 `null` 的情况而不会抛出错误。

React 会为每个返回的元素自动分配一个新的、稳定的 key，这对于在更新期间保留状态和优化性能至关重要。这些 key 是根据原始子元素的 key 及其在结构中的位置构建的。

**语法**
```javascript
React.Children.map(children, function(child, index))
```

**示例**

这个 `List` 组件将其每个子元素都包裹在一个 `<li>` 元素中。

```jsx
import { Children } from 'react';

function List({ children }) {
  return (
    <ul>
      {Children.map(children, (child, index) => (
        <li key={index}>{child}</li>
      ))}
    </ul>
  );
}

function App() {
  return (
    <List>
      <span>第一项</span>
      <span>第二项</span>
      <span>第三项</span>
    </List>
  );
}
```

## `React.Children.forEach`

类似于 `React.Children.map()`，但不返回数组。当需要遍历 children 集合而不想创建一个新集合时，这个方法很有用。

**语法**
```javascript
React.Children.forEach(children, function(child, index))
```

**示例**

该组件遍历其子元素，并将它们的类型打印到控制台。

```jsx
import { Children } from 'react';

function ChildLogger({ children }) {
  Children.forEach(children, (child, index) => {
    console.log(`索引为 ${index} 的子元素类型为：`, child.type);
  });

  return <div>{children}</div>;
}

function App() {
  return (
    <ChildLogger>
      <h1>标题</h1>
      <p>段落</p>
    </ChildLogger>
  );
}
```

## `React.Children.count`

返回 `children` 中的组件总数，该数量等于传递给 `map` 或 `forEach` 的回调函数将被调用的次数。

**语法**
```javascript
React.Children.count(children)
```

**示例**

```jsx
import { Children } from 'react';

function ItemCounter({ children }) {
  const count = Children.count(children);
  return <div>该组件有 {count} 个子元素。</div>;
}

function App() {
  return (
    <ItemCounter>
      <p>项目 1</p>
      <p>项目 2</p>
    </ItemCounter>
  );
  // 渲染结果：<div>该组件有 2 个子元素。</div>
}
```

## `React.Children.toArray`

将 `children` 这个不透明的数据结构以扁平化数组的形式返回，并为每个子元素分配 key。如果你想在渲染方法中操作 children 集合，特别是当你想对 `props.children` 进行重新排序或切片时，这个方法很有用。

**语法**
```javascript
React.Children.toArray(children)
```

**示例**

该组件接收其子元素，将它们转换为数组，并以相反的顺序渲染它们。

```jsx
import { Children } from 'react';

function ReverseOrder({ children }) {
  const childArray = Children.toArray(children);
  return <div>{childArray.reverse()}</div>;
}

function App() {
  return (
    <ReverseOrder>
      <span>一</span>
      <span>二</span>
      <span>三</span>
    </ReverseOrder>
  );
  // 以三、二、一的顺序渲染 span
}
```

## `React.Children.only`

验证 `children` 是否只有一个子元素（一个 React 元素），并返回该元素。如果 `children` 不是单个 React 元素，此函数将抛出错误。它不接受包含单个元素的数组；子元素必须被直接传递。

**语法**
```javascript
React.Children.only(children)
```

**示例**

该组件确保它只接收一个子元素。

```jsx
import { Children } from 'react';

function SingleChildWrapper({ children }) {
  // 如果传入多个子元素，这里会抛出错误。
  const singleChild = Children.only(children);
  
  // 现在你可以安全地克隆或检查这个唯一的子元素。
  return <div style={{ border: '1px solid red' }}>{singleChild}</div>;
}

function App() {
  return (
    <SingleChildWrapper>
      <p>这是唯一允许的子元素。</p>
    </SingleChildWrapper>
  );
}
```

---

通过使用这些工具函数，你可以构建出灵活且功能强大的组件，从而正确处理传递给它们的任何 children。要了解有关组件如何接收数据的更多信息，请参阅 [组件与 Props](./core-apis-components-and-props.md) 指南。