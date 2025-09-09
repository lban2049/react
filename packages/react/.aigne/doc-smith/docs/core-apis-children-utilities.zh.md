# Children 工具

在 React 中，`props.children` 是一个特殊的 prop，它允许组件进行组合。虽然你可以将任何内容作为 children 传递——从单个 JSX 元素到元素数组，甚至是函数——但实际的数据结构是不透明的。你不应该依赖于它是一个特定的类型，比如数组，因为它的实现可能会改变。

为了以安全和可预测的方式使用 `props.children`，React 提供了 `React.Children` 工具对象。这些辅助函数允许你迭代、映射、计数和操作 children prop，而无需做出不安全的假设。

这在设计可重用组件（如列表、布局或需要与传递给它们的元素进行交互的包装器）时特别有用。

## `React.Children.map()`

对 `children` 中包含的每个直接子元素调用一个函数，返回一个包含结果的新数组。如果 `children` 是 `null` 或 `undefined`，该方法将返回 `null` 或 `undefined`，而不是一个数组。

### 语法

```javascript
React.Children.map(children, function(child, index) { /* ... */ }, [context])
```

### 参数

| 名称 | 类型 | 描述 |
|---|---|---|
| `children`| `ReactNodeList` | 要迭代的 `props.children` 对象。 |
| `function`| `Function` | 为每个子元素执行的函数。它接收 `child` 及其 `index`。 |
| `context` | `any` | 可选。该函数的 `this` 上下文。 |

### 返回值

一个数组，其中包含对每个子元素调用回调函数的返回值。

### 示例

这对于修改子元素的 props 很有用。以下示例克隆每个子元素并添加一个新的 CSS 类。

```javascript React Component icon=logos:react
function RowList({ children }) {
  return (
    <div className="row-list">
      {React.Children.map(children, child =>
        React.cloneElement(child, {
          className: `${child.props.className || ''} list-item`
        })
      )}
    </div>
  );
}

// 用法
<RowList>
  <p>Item 1</p>
  <p className="special">Item 2</p>
  <div>Item 3</div>
</RowList>
```

## `React.Children.forEach()`

与 `React.Children.map()` 类似，但它不返回数组。它用于迭代子元素而不转换它们。

### 语法

```javascript
React.Children.forEach(children, function(child, index) { /* ... */ }, [context])
```

### 参数

| 名称 | 类型 | 描述 |
|---|---|---|
| `children`| `ReactNodeList` | 要迭代的 `props.children` 对象。 |
| `function`| `Function` | 为每个子元素执行的函数。它接收 `child` 及其 `index`。 |
| `context` | `any` | 可选。该函数的 `this` 上下文。 |

### 示例

```javascript React Component icon=logos:react
function ChildLogger({ children }) {
  React.Children.forEach(children, (child, index) => {
    console.log(`Child at index ${index} is:`, child);
  });

  return <div>{children}</div>;
}
```

## `React.Children.count()`

返回 `children` 中的组件总数，该数量等于传递给 `map` 或 `forEach` 的回调函数将被调用的次数。

### 语法

```javascript
React.Children.count(children)
```

### 参数

| 名称 | 类型 | 描述 |
|---|---|---|
| `children`| `ReactNodeList` | 要计数的 `props.children` 对象。 |

### 示例

```javascript React Component icon=logos:react
function ItemCounter({ children }) {
  const count = React.Children.count(children);
  return <div>There are {count} items.</div>;
}

// 用法
<ItemCounter>
  <span>Apple</span>
  <span>Banana</span>
  <span>Orange</span>
</ItemCounter>
// 渲染结果：<div>There are 3 items.</div>
```

## `React.Children.toArray()`

将 `children` 这个不透明的数据结构作为扁平数组返回，并为每个子元素分配 key。如果你想在渲染方法中操作子元素集合，特别是当你想要重新排序或分割 `props.children` 时，这非常有用。

### 示例

此示例获取一组子元素并以相反的顺序渲染它们。

```javascript React Component icon=logos:react
function ReverseOrder({ children }) {
  const childArray = React.Children.toArray(children);
  
  return (
    <div>
      {childArray.reverse()}
    </div>
  );
}

// 用法
<ReverseOrder>
  <p key="A">First</p>
  <p key="B">Second</p>
  <p key="C">Third</p>
</ReverseOrder>
// 渲染 "Third"，然后是 "Second"，最后是 "First"
```

## `React.Children.only()`

验证 `children` 只有一个子元素（一个 React 元素）并返回它。否则，此方法将抛出错误。这对于创建必须作为单个元素包装器的组件很有用。

### 语法

```javascript
React.Children.only(children)
```

### 示例

```javascript React Component icon=logos:react
function SingleChildWrapper({ children }) {
  // 如果 children 不是单个 React 元素，这将抛出错误。
  const singleChild = React.Children.only(children);

  // 现在你可以安全地使用 singleChild
  return (
    <div style={{ border: '1px solid red' }}>
      {singleChild}
    </div>
  );
}

// 有效用法：
<SingleChildWrapper>
  <p>Hello world</p>
</SingleChildWrapper>

// 无效用法（将抛出错误）：
<SingleChildWrapper>
  <p>One</p>
  <p>Two</p>
</SingleChildWrapper>
```

---

通过使用这些工具，你可以构建健壮且灵活的组件，以正确处理传递给它们的任何类型的 children。

接下来，你可以学习如何使用 [Refs](./core-apis-refs.md) 直接访问 DOM 节点或 React 组件。