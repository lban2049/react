# Children 工具

在 React 中，`props.children` 是一个特殊的 prop，它允许组件进行组合。虽然它通常看起来像一个 React 元素的数组，但它其实是一个不透明的数据结构。这意味着你不应该假定它是一个数组并直接使用 `children.map()` 等方法，因为 `props.children` 可能是单个元素、字符串、数字或 undefined。

为了安全且可预测地使用 `props.children`，React 在 `React.Children` 对象上提供了一套工具。这些辅助函数允许你遍历、计数和转换 children prop，而无需关心其底层结构。

```d2
direction: down

props-children: {
  label: "props.children\n（不透明数据结构）"
  shape: package

  child-1: {
    label: "单个元素\n<div />"
    shape: rectangle
  }
  child-2: {
    label: "元素数组\n[<p />, <span />]"
    shape: rectangle
  }
  child-3: {
    label: "字符串或数字\n'Hello'"
    shape: rectangle
  }
}

react-children-api: {
  label: "React.Children 工具\n（.map、.forEach、.toArray）"
  shape: hexagon
}

processed-array: {
  label: "可预测数组\n（扁平化，带稳定 key）"
  shape: package

  element-1: {
    label: "<div key='...' />"
    shape: rectangle
  }
  element-2: {
    label: "<p key='...' />"
    shape: rectangle
  }
  element-3: {
    label: "<span key='...' />"
    shape: rectangle
  }
  element-4: {
    label: "'Hello'"
    shape: rectangle
  }
}

props-children -> react-children-api: "处理"
react-children-api -> processed-array: "返回"
```

## `React.Children.map`

`React.Children.map(children, function(child, index), [thisArg])`

对 `children` 中包含的每个直接子元素调用一个函数。其功能与标准的 `Array.prototype.map()` 方法非常相似，但对任何类型的 `props.children` 都是安全的。返回的值是一个由回调函数的结果组成的数组。

`React.Children.map` 的一个关键特性是它会自动处理返回数组中每个元素的 `key` prop，这对于高效渲染和在更新期间维护状态至关重要。

**示例：将每个子元素包裹在列表项中**
```jsx
import { Children } from 'react';

function List({ children }) {
  return (
    <ul>
      {Children.map(children, (child) => (
        <li>{child}</li>
      ))}
    </ul>
  );
}

function App() {
  return (
    <List>
      <span>Apple</span>
      <span>Banana</span>
      <span>Orange</span>
    </List>
  );
}
```

## `React.Children.forEach`

`React.Children.forEach(children, function(child, index), [thisArg])`

与 `React.Children.map()` 类似，但它不返回数组。它用于遍历子元素而不转换它们，例如用于日志记录或其他副作用。

**示例：记录每个子元素的类型**
```jsx
import { Children } from 'react';

function ChildLogger({ children }) {
  Children.forEach(children, (child, index) => {
    // 注意：对于非元素子节点（如字符串），child.type 可能不存在。
    if (child && child.type) {
      console.log(`Child at index ${index} is a`, child.type);
    }
  });

  return <div>{children}</div>;
}

function App() {
  return (
    <ChildLogger>
      <p>First</p>
      <div />
    </ChildLogger>
  );
}
```

## `React.Children.count`

`React.Children.count(children)`

返回 `children` 中的组件总数，该数量等于传递给 `map` 或 `forEach` 的回调函数将被调用的次数。

**示例：显示项目计数**
```jsx
import { Children } from 'react';

function ItemCounter({ children }) {
  const count = Children.count(children);
  return <h3>There are {count} items.</h3>;
}

function App() {
  return (
    <ItemCounter>
      <div />
      <div />
    </ItemCounter>
  );
}
```

## `React.Children.toArray`

`React.Children.toArray(children)`

将 `children` 这个不透明的数据结构作为扁平数组返回，并为每个子元素分配 key。如果你想在渲染前操作子元素集合（例如重新排序或切片），这个方法会非常有用。

**示例：反转并渲染子元素**
```jsx
import { Children } from 'react';

function ReversedList({ children }) {
  const childArray = Children.toArray(children);
  
  // 反转数组并渲染
  return <div>{childArray.reverse()}</div>;
}

function App() {
  return (
    <ReversedList>
      <span>One</span>
      <span>Two</span>
      <span>Three</span>
    </ReversedList>
  );
}
```

## `React.Children.only`

`React.Children.only(children)`

验证 `children` 是否只有一个子元素（一个 React 元素）并返回它。如果 `children` 不是单个 React 元素，此函数将抛出错误。这对于创建那些设计为包裹单个子元素的组件非常有用。

**示例：需要单个子元素的组件**
```jsx
import { Children } from 'react';

function SingleChildWrapper({ children }) {
  // 如果传递了多个子元素，此行会抛出错误。
  const child = Children.only(children);
  
  // 现在你可以安全地克隆和修改这个唯一的子元素。
  return <div style={{ border: '1px solid red' }}>{child}</div>;
}

function App() {
  // 这样可行：
  // return <SingleChildWrapper><p>Hello</p></SingleChildWrapper>;

  // 这将抛出错误：
  return (
    <SingleChildWrapper>
      <p>Hello</p>
      <p>World</p>
    </SingleChildWrapper>
  );
}
```

通过使用这些工具，你可以构建灵活且健壮的组件，以正确处理传递给它们的任何子元素组合。

掌握了如何使用 children 后，你可能想学习如何访问其底层的 DOM 节点或组件实例。请继续阅读下一节关于 [Refs](./core-apis-refs.md) 的内容以了解更多。