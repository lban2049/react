# 服务器实用程序

React 服务器组件利用 React 核心库中的一组实用函数。这些实用程序提供了在服务器环境中操作 React 元素和管理组件行为的基本能力。

有关服务器端组件开发的 React API 和 Hooks 的详细信息，请参阅 [服务器 Hooks](./server-components-apis-hooks.md)。

## Children

`React.Children` 对象提供了处理 `props.children` 不透明数据结构的实用工具。这些函数帮助您安全地迭代、转换和计数传递给组件的子元素，无论它们是单个元素、元素数组，甚至是复杂的嵌套结构。

### `React.Children.map`

此函数遍历 `children` 中的每个直接子元素，并对每个子元素调用 `func` 函数。如果 `children` 是一个数组，则 `func` 将对数组中的每个子元素调用。如果 `children` 为 `null` 或 `undefined`，则此方法分别返回 `null` 或 `undefined`。

**Parameters**

| Name | Type | Description |
|---|---|---|
| `children` | `?ReactNodeList` | 要迭代的子元素，通常为 `props.children`。可以是单个节点、数组或可迭代对象。 |
| `func` | `(child: ?React$Node, index: number) => ?ReactNodeList` | 对每个子元素调用的函数。它接收 `child` 及其 `index` 作为参数。 |
| `context` | `mixed` | `func` 的 `this` 上下文。 |

**Returns**

| Name | Type | Description |
|---|---|---|
| `result` | `?Array<React$Node>` | 一个数组，包含将 `func` 应用于每个子元素的结果。如果 `children` 为 `null` 或 `undefined`，则返回 `null` 或 `undefined`。 |

**Example**

```javascript
import { Children } from 'react';

function ListItemRenderer({ children }) {
  return (
    <ul>
      {Children.map(children, (child, index) => (
        <li key={index}>
          {child}
        </li>
      ))}
    </ul>
  );
}

// Example usage in a Server Component
function MyServerComponent() {
  return (
    <ListItemRenderer>
      <span>Item 1</span>
      <span>Item 2</span>
      Text Node 3
    </ListItemRenderer>
  );
}
```

此示例将 `ListItemRenderer` 的每个子元素包装在 `<li>` 元素中，演示了如何转换子元素。

### `React.Children.forEach`

类似于 `map`，但它不返回新数组。它遍历每个直接子元素，并对每个子元素调用 `forEachFunc`。当您需要在不修改子元素的情况下对每个子元素执行操作时，此方法很有用。

**Parameters**

| Name | Type | Description |
|---|---|---|
| `children` | `?ReactNodeList` | 要迭代的子元素。 |
| `forEachFunc` | `(child: ?React$Node, index: number) => void` | 对每个子元素调用的函数。它接收 `child` 及其 `index`。 |
| `forEachContext` | `mixed` | `forEachFunc` 的 `this` 上下文。 |

**Example**

```javascript
import { Children } from 'react';

function LogChildren({ children }) {
  Children.forEach(children, (child, index) => {
    console.log(`Child at index ${index}:`, child);
  });
  return null; // Or return children directly
}

// Example usage
function App() {
  return (
    <LogChildren>
      <p>Hello</p>
      <span>World</span>
    </LogChildren>
  );
}
```

此示例遍历子元素，并将每个子元素记录到控制台。

### `React.Children.count`

返回 `children` 中组件的总数，等同于 `map` 或 `forEach` 回调将被调用的次数。

**Parameters**

| Name | Type | Description |
|---|---|---|
| `children` | `?ReactNodeList` | 要计数的子元素。 |

**Returns**

| Name | Type | Description |
|---|---|---|
| `count` | `number` | 子元素总数。 |

**Example**

```javascript
import { Children } from 'react';

function ChildrenCounter({ children }) {
  const count = Children.count(children);
  return <p>This component has {count} children.</p>;
}

// Example usage
function App() {
  return (
    <ChildrenCounter>
      <span>One</span>
      <span>Two</span>
      <span>Three</span>
    </ChildrenCounter>
  );
}
```

此示例计数子元素的数量并显示它。

### `React.Children.toArray`

将子元素对象（通常指定为 `props.children`）扁平化为数组。此函数特别适用于在将子元素传递给另一个组件之前对其进行重新排序或切片。

**Parameters**

| Name | Type | Description |
|---|---|---|
| `children` | `?ReactNodeList` | 要扁平化为数组的子元素。 |

**Returns**

| Name | Type | Description |
|---|---|---|
| `array` | `Array<React$Node>` | 包含所有子元素的数组。 |

**Example**

```javascript
import { Children } from 'react';

function ReverseChildren({ children }) {
  const childrenArray = Children.toArray(children);
  const reversedChildren = childrenArray.slice().reverse();
  return (
    <div>
      {reversedChildren}
    </div>
  );
}

// Example usage
function App() {
  return (
    <ReverseChildren>
      <div>First</div>
      <div>Second</div>
      <div>Third</div>
    </ReverseChildren>
  );
}
```

此示例将子元素转换为数组，反转其顺序，然后渲染它们。

### `React.Children.only`

验证 `children` 是否只有一个子元素（一个 React 元素）并返回它。否则，此方法将抛出错误。

**Parameters**

| Name | Type | Description |
|---|---|---|
| `children` | `T` | 要检查的子元素对象。 |

**Returns**

| Name | Type | Description |
|---|---|---|
| `child` | `T` | 单个 React 元素子元素。 |

**Example**

```javascript
import { Children } from 'react';

function SingleChildWrapper({ children }) {
  const singleChild = Children.only(children);
  return (
    <div style={{ border: '1px solid blue' }}>
      {singleChild}
    </div>
  );
}

// Example usage (will work)
function App() {
  return (
    <SingleChildWrapper>
      <span>I am the only child.</span>
    </SingleChildWrapper>
  );
}

// Example usage (will throw an error if uncommented)
/*
function AppWithError() {
  return (
    <SingleChildWrapper>
      <span>Child 1</span>
      <span>Child 2</span>
    </SingleChildWrapper>
  );
}
*/
```

此示例展示了 `only` 如何确保组件只接收一个子元素。

## createElement

`React.createElement` 函数创建并返回给定 `type` 的新 React 元素。这是 JSX 背后的核心函数，JSX 是 `createElement` 调用的语法糖。当您编写 JSX 时，它会被转译为 `React.createElement` 调用。

**Parameters**

| Name | Type | Description |
|---|---|---|
| `type` | `any` | 元素的类型。可以是字符串（用于 DOM 元素，如 `'div'`、`'span'`）、React 组件类或函数组件。 |
| `config` | `object` | 一个对象，包含元素的 `props` 以及 `key`/`ref`（如果存在）。这些将被传递给组件。 |
| `children` | `...any` | 其余参数被视为元素的子元素。它们可以是元素、字符串、数字或 `null`。 |

**Returns**

| Name | Type | Description |
|---|---|---|
| `element` | `ReactElement` | 一个新的 React 元素对象。 |

**Example**

```javascript
import { createElement } from 'react';

function MyButton(props) {
  return createElement('button', { className: 'my-btn', onClick: props.onClick }, props.children);
}

// Using createElement to create a div with a custom button and text
function App() {
  return createElement(
    'div',
    { style: { padding: '20px', border: '1px solid gray' } },
    createElement(MyButton, { onClick: () => console.log('Button clicked!') }, 'Click Me'),
    'Hello, React!',
  );
}
```

此示例演示了如何使用 `createElement` 以编程方式创建元素，这也是 JSX 在底层编译后的结果。

## cloneElement

`React.cloneElement` 函数以 `element` 为起点创建并返回一个新的 React 元素。新元素将具有与原始元素相同的 `type` 和 `key`，但其 `props` 将是原始元素的 `props` 与新的 `config` 和 `children` 的浅合并。这对于修改其他组件创建的元素而无需直接改变它们很有用。

**Parameters**

| Name | Type | Description |
|---|---|---|
| `element` | `ReactElement` | 要克隆的现有 React 元素。 |
| `config` | `object` | 一个对象，包含要与原始元素 `props` 合并的新 `props`。也可以包含新的 `key` 或 `ref`。 |
| `children` | `...any` | 替换原始元素子元素的新子元素。如果提供，它们将覆盖原始子元素。 |

**Returns**

| Name | Type | Description |
|---|---|---|
| `clonedElement` | `ReactElement` | 一个新的 React 元素，从原始元素克隆并修改。 |

**Example**

```javascript
import { createElement, cloneElement } from 'react';

function Item({ id, children }) {
  return createElement('div', null, `Item ${id}: `, children);
}

function Wrapper({ children }) {
  const enhancedChildren = cloneElement(children, {
    id: 'Enhanced', // Override or add a prop
    style: { color: 'blue' }
  }, ' (Cloned and enhanced)'); // Replace existing children with new ones

  return createElement('div', null, enhancedChildren);
}

// Example usage
function App() {
  return createElement(Wrapper, null, createElement(Item, { id: 'Original' }, 'Content'));
}
```

此示例演示了如何使用 `cloneElement` 为现有元素添加新 `props` 或更改子元素，而无需修改原始元素。

## isValidElement

`React.isValidElement` 函数验证一个对象是否为 React 元素。React 元素是一个普通的 JavaScript 对象，它描述了一个组件实例或 DOM 节点及其所需的属性。

**Parameters**

| Name | Type | Description |
|---|---|---|
| `object` | `any` | 要检查的对象。 |

**Returns**

| Name | Type | Description |
|---|---|---|
| `boolean` | `boolean` | 如果 `object` 是 React 元素则为 `true`，否则为 `false`。 |

**Example**

```javascript
import { createElement, isValidElement } from 'react';

const myElement = createElement('div', null, 'Hello');
const myObject = { name: 'test' };

console.log(isValidElement(myElement)); // true
console.log(isValidElement(myObject));  // false
console.log(isValidElement(null));      // false
console.log(isValidElement('string'));  // false
```

此示例说明了如何使用 `isValidElement` 来确定给定变量是否持有有效的 React 元素。

## createRef

`React.createRef` 函数是 React 的一个核心实用工具，用于创建一个 `ref` 对象。`ref` 对象是可变容器，具有单个 `current` 属性，可以持有对 DOM 元素或组件实例的引用。尽管通常与客户端 DOM 操作相关联，但 `createRef` 可用于同构代码或需要可变引用的内部组件逻辑中，不过它在纯服务器组件中的直接应用较少见，因为它们不直接与 DOM 交互。

**Returns**

| Name | Type | Description |
|---|---|---|
| `refObject` | `RefObject` | 一个对象，其单个 `current` 属性初始化为 `null`。 |

**Example**

```javascript
import { createRef } from 'react';

function MyComponent() {
  const myRef = createRef(); // Create a ref object

  // In a client-side component, you might attach it to a DOM element:
  // <input ref={myRef} />

  // In a server component context, `myRef.current` will typically remain null
  // as there's no DOM to attach to. However, the ref object itself can be passed around
  // if you're building a library that needs to handle refs in a consistent way.
  console.log(myRef.current); // null
  return null;
}
```

此示例展示了如何创建 `ref` 对象。在服务器组件中，`myRef.current` 将保持 `null`，因为没有浏览器 DOM。

## lazy

`React.lazy` 函数允许您延迟加载组件代码，直到首次渲染该组件。此功能主要用于客户端应用程序的代码分割，使您能够在需要时才加载组件。尽管 `lazy` 本身在服务器组件中可用，但它在代码分割方面的实际用途通常是在客户端，因为服务器组件渲染为静态 HTML 或序列化表示。然而，了解其机制仍然很重要，因为它可以向下传递给客户端组件，或者在服务器组件可能引用延迟加载的客户端组件的场景中使用。

**Parameters**

| Name | Type | Description |
|---|---|---|
| `ctor` | `() => Thenable<{default: T, ...}>` | 一个函数，返回一个 `Thenable`（如 Promise），它解析为一个模块对象。该模块对象必须具有一个包含 React 组件的 `default` 导出。 |

**Returns**

| Name | Type | Description |
|---|---|---|
| `LazyComponent` | `LazyComponent<T, Payload<T>>` | 一个可以像常规组件一样渲染的 React 组件。 |

**Example**

```javascript
import { lazy } from 'react';

// This is typically used for client components for code-splitting
// const LazyClientComponent = lazy(() => import('./ClientComponent'));

function ServerDisplayComponent() {
  // Although `lazy` is available on the server, the actual lazy loading
  // (fetching the code) only happens on the client side when the component is rendered.
  // A Server Component would usually just pass this `LazyClientComponent` reference
  // to a Client Component to be rendered there.
  return (
    <div>
      {/* <LazyClientComponent /> is an example for client-side usage */}
      <p>React.lazy is primarily for client-side code-splitting.</p>
    </div>
  );
}
```

此示例说明了 `lazy` 函数的签名及其在客户端的主要用例，尽管该 API 本身在服务器运行时中也已公开。

## memo

`React.memo` 函数是一个高阶组件，允许您记忆化（memoize）函数组件。如果您的组件在给定相同 `props` 的情况下渲染出相同的结果，您可以将其包装在 `React.memo` 中以在某些情况下获得性能提升。它可以在组件的 `props` 未更改时防止其重新渲染。此优化主要有利于客户端渲染，以避免浏览器 DOM 中不必要的重新渲染。在服务器组件中，组件一次性渲染为静态输出，`memo` 的性能优势显著降低，但为了保持一致性或组件同时用于客户端和服务器边界时，它仍然可用。

**Parameters**

| Name | Type | Description |
|---|---|---|
| `type` | `React$ElementType` | 要记忆化的组件类型。这应该是一个函数组件。 |
| `compare` | `(oldProps: Props, newProps: Props) => boolean` | 一个可选的比较函数。如果提供，React 将使用它来比较旧 `props` 和新 `props`。如果返回 `true`，则组件不会重新渲染。如果省略，React 将执行 `props` 的浅比较。 |

**Returns**

| Name | Type | Description |
|---|---|---|
| `ElementType` | `ElementType` | 一个记忆化的 React 组件。 |

**Example**

```javascript
import { memo } from 'react';

function MyMemoizedComponent({ name, count }) {
  // This component will only re-render if 'name' or 'count' props change
  // (or if React.memo uses a custom 'compare' function).
  console.log('MyMemoizedComponent rendered!');
  return (
    <div>
      <p>Name: {name}</p>
      <p>Count: {count}</p>
    </div>
  );
}

const MemoizedComponent = memo(MyMemoizedComponent);

// In a Server Component, MemoizedComponent is rendered once to HTML.
// The memoization benefit (avoiding re-renders) is primarily for client-side updates.
function ServerRenderingMemo() {
  return (
    <div>
      <MemoizedComponent name="Alice" count={1} />
      <MemoizedComponent name="Bob" count={2} />
    </div>
  );
}
```

此示例展示了如何使用 `memo` 创建记忆化组件。尽管在服务器组件中可用，但其主要性能影响是在客户端更新期间观察到的。

---

本节概述了服务器组件环境中可用的基本 React 实用函数。这些工具能够有效地操作和管理 React 元素，为健壮的应用程序开发奠定了基础。

要继续探索 React 服务器组件的功能，请继续了解 [Taint Registry](./server-components-apis-taint-registry.md)，以了解敏感数据在服务器环境中是如何管理的。
