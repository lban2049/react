# Refs

Refs 提供了一种访问在 `render` 方法中创建的 DOM 节点或 React 组件的方式。在典型的 React 数据流中，props 是父组件与子组件交互的唯一方式。要修改子组件，你需要使用新的 props 重新渲染它。然而，在某些情况下，你需要在典型数据流之外强制修改子组件。需要修改的子组件可能是一个 React 组件的实例，也可能是一个 DOM 元素。

虽然你可以为 class 组件添加 ref，但你不能在函数组件上使用 `ref` 属性，因为它们没有实例。但是，你可以使用 `forwardRef` 将 ref 透传给函数组件内部的 DOM 元素或 class 组件。

对于在函数组件中管理 refs，推荐使用 `useRef` Hook。你可以在 [Ref Hooks](./hooks-ref.md) 部分了解更多相关信息。

## `createRef`

`createRef` 会创建一个 ref 对象，该对象可以通过 `ref` 属性附加到 React 元素上。该函数返回一个可变的 ref 对象，其 `.current` 属性被初始化为 `null`。当 `ref` 属性被用于一个元素时，`.current` 属性将被更新为相应的 DOM 节点或组件实例。

在内部，`createRef` 是一个返回对象的简单函数，正如其实现所示：

```javascript ReactCreateRef.js icon=logos:javascript
export function createRef(): RefObject {
  const refObject = {
    current: null,
  };
  // 在开发环境中，该对象被密封以防止意外修改。
  if (__DEV__) {
    Object.seal(refObject);
  }
  return refObject;
}
```

### Class 组件中的用法

Refs 通常在构造函数中被赋值给一个实例属性，以便在整个组件中引用它们。

```javascript MyComponent.js icon=logos:react
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    // 创建一个 ref 来存储 textInput DOM 元素
    this.textInput = React.createRef();
  }

  componentDidMount() {
    // 使用 .current 属性访问 DOM 节点并聚焦输入框
    this.textInput.current.focus();
  }

  render() {
    // 将 ref 附加到 <input> 元素
    return (
      <input
        type="text"
        ref={this.textInput} />
    );
  }
}
```

在这个例子中，当组件挂载时，React 会将 DOM 元素赋值给 `this.textInput.current`。

## `forwardRef`

Ref 转发是一种将 ref 自动地通过组件传递到其子组件的技术。这对于可复用的组件库和父组件需要直接访问子组件 DOM 节点的场景尤其有用。

`React.forwardRef` 接受一个渲染函数作为其参数。React 会以 `props` 和 `ref` 作为两个参数来调用这个函数。这个 `ref` 来自父组件，可以被转发到子组件内部的一个元素。

```javascript ReactForwardRef.js icon=logos:javascript
export function forwardRef<Props, ElementType: React$ElementType>(
  render: (
    props: Props,
    ref: React$RefSetter<React$ElementRef<ElementType>>,
  ) => React$Node,
) {
  // ...（为简洁起见，省略了开发环境检查）
  const elementType = {
    $$typeof: REACT_FORWARD_REF_TYPE,
    render,
  };
  return elementType;
}
```

### 示例：将 ref 转发到 DOM 元素

下面是一个 `FancyButton` 函数组件的示例，它使用 `forwardRef` 将接收到的 `ref` 传递给底层的 DOM `<button>` 元素。

```javascript FancyButton.js icon=logos:react
const FancyButton = React.forwardRef((props, ref) => (
  <button ref={ref} className="FancyButton">
    {props.children}
  </button>
));

// 现在你可以获取底层 DOM 按钮的 ref：
const ref = React.createRef();
const App = () => (
  <FancyButton ref={ref}>Click me!</FancyButton>
);

// 渲染后，ref.current 将指向 <button> DOM 节点。
```

以上示例的执行过程如下：
1. 我们通过调用 `React.createRef` 创建一个 React ref，并将其赋值给 `ref` 变量。
2. 我们通过将其指定为 JSX 属性，将 `ref` 向下传递给 `<FancyButton>`。
3. React 将 `ref` 作为第二个参数传递给 `forwardRef` 内部的 `(props, ref) => ...` 函数。
4. 我们通过将其指定为 JSX 属性，将这个 `ref` 参数向下转发给 `<button>`。
5. 当 ref 被附加时，`ref.current` 将指向 `<button>` DOM 节点。

## 何时使用 Refs

下面是一些 refs 的适用场景：

*   **管理焦点、文本选择或媒体播放。**
*   **触发强制性动画。**
*   **与第三方 DOM 库集成。**

避免将 refs 用于任何可以通过声明式方式完成的事情。例如，不要在 `Dialog` 组件上暴露 `open()` 和 `close()` 方法，而是传递一个 `isOpen` prop 给它。

---

以上内容涵盖了在 class 组件中创建和转发 refs，以及在函数组件中透传 refs 的核心 API。若想了解在函数组件中基于 hook 的现代方法，请参阅下一部分的推荐阅读。

<x-cards>
  <x-card data-title="Ref Hooks" data-icon="lucide:hook" data-href="/hooks/ref">
    了解用于在函数组件中管理 refs 的 `useRef` 和 `useImperativeHandle` Hook。
  </x-card>
  <x-card data-title="Context" data-icon="lucide:box" data-href="/core-apis/context">
    探索如何通过组件树传递数据，而无需手动向下传递 props。
  </x-card>
</x-cards>