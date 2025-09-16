# React.Children

`React.Children` 工具提供了处理 `props.children` 数据结构的方法。在 React 中，`props.children` 是一个不透明的数据结构，这意味着你不应假定它是一个简单的数组。它可以是单个元素、元素数组、字符串、数字，甚至是 `undefined`。

这些工具可以帮助你安全地迭代、计数和转换 `children` prop，而无需对其底层结构进行任何假设。这在设计需要操作所接收元素的可重用组件时尤其有用。

若要了解更多基础概念，可以查阅 [组件与类](./api-reference-components.md)。

## map()

该方法会对 `children` 中包含的每个直接子元素调用一个函数，并返回一个包含结果的数组。这与原生 `Array.prototype.map()` 方法类似，但它能正确处理 `props.children` 可能的各种类型。

如果 `children` 是一个 `Fragment`，它将被遍历，但数组不会被展平。返回的数组将包含对每个子元素调用 `mapFn` 的结果。

### 参数

<x-field data-name="children" data-type="ReactNodeList" data-required="true" data-desc="要迭代的 props.children 对象。"></x-field>
<x-field data-name="mapFn" data-type="(child: React.Node, index: number) => any" data-required="true" data-desc="将为每个子元素调用的函数。它接收子元素及其索引作为参数。"></x-field>
<x-field data-name="context" data-type="any" data-required="false" data-desc="执行 mapFn 时使用的 'this' 上下文。"></x-field>

### 返回值

<x-field data-name="mappedChildren" data-type="array" data-desc="一个数组，其中包含对每个子元素调用 mapFn 的返回值。"></x-field>

### 示例

这是一个将每个子元素包裹在 `<li>` 元素中的组件。

```javascript ListWrapper Component icon=logos:javascript
import React from 'react';

function ListWrapper({ children }) {
  return (
    <ul>
      {React.Children.map(children, (child, index) => (
        <li key={index}>{child}</li>
      ))}
    </ul>
  );
}

// Usage:
// <ListWrapper>
//   <span>First Item</span>
//   <span>Second Item</span>
// </ListWrapper>
//
// Renders:
// <ul>
//   <li><span>First Item</span></li>
//   <li><span>Second Item</span></li>
// </ul>
```

## forEach()

与 `React.Children.map()` 类似，但它不返回数组。它会遍历 `children` 中的每个直接子元素，并为每个子元素调用所提供的函数。当你需要为每个子元素执行一个函数，但又不需要将它们转换成一个新数组时，可以使用此方法。

### 参数

<x-field data-name="children" data-type="ReactNodeList" data-required="true" data-desc="要迭代的 props.children 对象。"></x-field>
<x-field data-name="forEachFunc" data-type="(child: React.Node, index: number) => void" data-required="true" data-desc="将为每个子元素调用的函数。"></x-field>
<x-field data-name="context" data-type="any" data-required="false" data-desc="执行 forEachFunc 时使用的 'this' 上下文。"></x-field>

### 返回值

此方法没有返回值。

### 示例

此示例会记录每个子元素的 `displayName` 或 `type`。

```javascript ChildLogger Component icon=logos:javascript
import React from 'react';

function ChildLogger({ children }) {
  React.Children.forEach(children, (child, index) => {
    if (React.isValidElement(child)) {
      console.log(`Child #${index} is a <${child.type.displayName || child.type}> element.`);
    }
  });

  return <div>{children}</div>;
}
```

## count()

返回 `children` 中组件的总数。这相当于传递给 `map()` 或 `forEach()` 的回调函数将被调用的次数。

### 参数

<x-field data-name="children" data-type="ReactNodeList" data-required="true" data-desc="要计数的 props.children 对象。"></x-field>

### 返回值

<x-field data-name="count" data-type="number" data-desc="子组件的数量。"></x-field>

### 示例

```javascript ItemCounter Component icon=logos:javascript
import React from 'react';

function ItemCounter({ children }) {
  const childCount = React.Children.count(children);
  return (
    <div>
      <h3>There are {childCount} items.</h3>
      {children}
    </div>
  );
}
```

## toArray()

将 `children` 数据结构作为扁平数组返回。当你需要将 `children` prop 视为标准数组以进行排序或切片等操作，同时又需要保留渲染所需的 `key` 时，此方法非常有用。

### 参数

<x-field data-name="children" data-type="ReactNodeList" data-required="true" data-desc="要转换为数组的 props.children 对象。"></x-field>

### 返回值

<x-field data-name="childrenArray" data-type="React.Node[]" data-desc="一个已分配 key 的扁平化 React 节点数组。"></x-field>

### 示例

此组件将其子元素转换为数组，并按相反顺序渲染它们。

```javascript ReversedList Component icon=logos:javascript
import React from 'react';

function ReversedList({ children }) {
  const childArray = React.Children.toArray(children);
  // Now we can safely use array methods
  const reversedChildren = childArray.reverse();

  return <>{reversedChildren}</>;
}

// Usage:
// <ReversedList>
//   <p>One</p>
//   <p>Two</p>
//   <p>Three</p>
// </ReversedList>
//
// Renders:
// <p>Three</p>
// <p>Two</p>
// <p>One</p>
```

## only()

验证 `children` 只有一个子元素（必须是有效的 React 元素）并返回它。如果 `children` 为空、`null`、`undefined` 或包含多个子元素，此函数将抛出错误。

这对于那些设计为包裹单个子元素的组件来说，是一种有用的验证方式。

### 参数

<x-field data-name="children" data-type="any" data-required="true" data-desc="要检查的 props.children 对象。"></x-field>

### 返回值

<x-field data-name="child" data-type="ReactElement" data-desc="唯一的 React 元素子节点。"></x-field>

### 示例

这个 `<Frame>` 组件要求在其边框内渲染单个子元素。

```javascript Frame Component icon=logos:javascript
import React from 'react';

function Frame({ children }) {
  // This will throw an error if more than one child is passed.
  const singleChild = React.Children.only(children);

  return (
    <div style={{ border: '2px solid blue', padding: '10px' }}>
      {singleChild}
    </div>
  );
}

// Valid usage:
// <Frame><p>Hello</p></Frame>

// Invalid usage (throws error):
// <Frame><p>One</p><p>Two</p></Frame>
// <Frame />
```