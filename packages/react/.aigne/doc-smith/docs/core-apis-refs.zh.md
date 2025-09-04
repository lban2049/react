# Refs

Refs 提供了一种访问在 `render` 方法中创建的 DOM 节点或 React 组件的方式。在典型的 React 数据流中，props 是父组件与子组件交互的唯一方式。要修改子组件，你需要使用新的 props 重新渲染它。然而，在某些情况下，你需要在典型的数据流之外命令式地修改子组件。需要修改的子组件可以是一个 React 组件的实例，也可以是一个 DOM 元素。

Refs 的常见用例包括：
- 管理焦点、文本选择或媒体播放。
- 触发命令式动画。
- 与第三方 DOM 库集成。

本节介绍了创建和转发 refs 的核心 API。对于函数组件，`useRef` hook 是现代且推荐的方法。你可以在 [Ref Hooks](./hooks-ref.md) 文档中了解更多信息。

## createRef

`React.createRef` 创建一个 ref 对象，该对象可以通过 `ref` 属性附加到 React 元素上。该函数返回一个可变的 ref 对象，其 `.current` 属性初始化为 `null`。当 `ref` 属性用于元素时，React 会将 DOM 元素或类组件实例分配给 ref 对象的 `.current` 属性。

`createRef` 的实现很简单；它返回一个简单的对象容器：

```javascript
// 一个具有单一可变值的不可变对象
export function createRef(): RefObject {
  const refObject = {
    current: null,
  };
  if (__DEV__) {
    Object.seal(refObject);
  }
  return refObject;
}
```

### Usage

Refs 通常在类组件的构造函数中分配给实例属性，以便在整个组件中引用它们。

**示例：聚焦输入元素**

在此示例中，我们在 `MyInput` 组件的构造函数中创建一个 ref，在 `render` 方法中将其附加到 `<input>` 元素，并在事件处理程序中使用它以编程方式聚焦输入。

```jsx
class MyInput extends React.Component {
  constructor(props) {
    super(props);
    // 创建一个 ref 来存储 textInput DOM 元素
    this.textInput = React.createRef();
    this.focusTextInput = this.focusTextInput.bind(this);
  }

  focusTextInput() {
    // 使用原生 DOM API 显式聚焦文本输入
    // 注意：我们正在访问 “current” 以获取 DOM 节点
    this.textInput.current.focus();
  }

  render() {
    // 使用 `ref` 回调将对文本输入 DOM 元素的引用存储
    // 在实例字段中（例如，this.textInput）。
    return (
      <div>
        <input
          type="text"
          ref={this.textInput} />
        <input
          type="button"
          value="Focus the text input"
          onClick={this.focusTextInput}
        />
      </div>
    );
  }
}
```

## forwardRef

默认情况下，你不能将 `ref` 属性传递给函数组件。`ref` 不是一个 prop。与 `key` 非常相似，它由 React 进行特殊处理。如果你想让父组件获取子组件内部 DOM 节点的 ref，你需要使用 `React.forwardRef`。

`React.forwardRef` 是一个高阶组件，它接受一个渲染函数。该函数接收 `props` 和 `ref` 作为参数，并返回一个 React 节点。然后，`ref` 可以被转发到组件内部的元素。

### Ref 转发的工作原理

下图说明了在父组件中创建的 ref 如何通过自定义的 `FancyButton` 组件转发到底层的 DOM `<button>` 元素。

```d2
direction: down

Parent-Component: {
  label: "父组件"
  shape: class

  create: {
    label: "1. const buttonRef = React.createRef();"
  }

  render: {
    label: "2. <FancyButton ref={buttonRef} />"
  }

  access: {
    label: "5. 通过 buttonRef.current 访问"
  }
}

FancyButton: {
  label: "FancyButton = forwardRef((props, ref) => ...)"
  shape: rectangle

  receive: {
    label: "3. 接收转发的 ref"
  }

  attach: {
    label: "4. 将 ref 附加到 DOM 元素：<button ref={ref} />"
  }
}

DOM-Button: {
  label: "DOM <button> 元素"
  shape: cylinder
}

Parent-Component.render -> FancyButton.receive: "传递 ref"
FancyButton.attach -> DOM-Button: "连接 ref"
DOM-Button -> Parent-Component.access: "填充 .current"

```

### Usage

这是一个 `FancyButton` 组件的示例，它使用 `forwardRef` 将 ref 传递给底层的按钮 DOM 元素：

```jsx
const FancyButton = React.forwardRef((props, ref) => (
  <button ref={ref} className="FancyButton">
    {props.children}
  </button>
));

// 现在你可以获取底层 DOM 按钮的 ref：
const ref = React.createRef();

function App() {
  // 在实际应用中，你可能会在某个操作后使用此 ref 来聚焦按钮。
  return <FancyButton ref={ref}>Click me!</FancyButton>;
}
```

在此示例中，`App` 组件将其 `ref` 向下传递给 `FancyButton`。然后，`FancyButton` 将相同的 `ref` 转发给它渲染的 `<button>` 元素。因此，`App` 组件中的 `ref.current` 将直接指向 `<button>` DOM 节点。

## 后续步骤

你现在已经了解了如何使用 React 的核心 API 创建和转发 refs。当声明式方法不适用时，这些是与 DOM 交互的重要工具。

- 要学习在函数组件中处理 refs 的现代方法，请参阅 [Ref Hooks](./hooks-ref.md) 文档。
- 要探索另一种通过组件树传递数据的方法，请查看 [Context](./core-apis-context.md) API。
