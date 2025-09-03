# JSX

JSX 是一种 JavaScript 的语法扩展，它允许你以类似 HTML 的格式编写 UI 描述。它提供了一种简洁而熟悉的方式来定义 React 元素和组件，使你的代码更具可读性和可维护性。

虽然 JSX 可能看起来像一种模板语言，但它完全由 JavaScript 驱动。它通过像 Babel 这样的工具被编译成常规的 JavaScript 函数调用。这意味着你可以直接在 UI 标记中使用 JavaScript 的全部功能，包括变量、循环和条件逻辑。

要深入了解 JSX 编译成的底层函数，请参阅关于[创建和操作元素](./core-apis-creating-elements.md)的指南。

## JSX 转换

现代 React 版本使用了一种新的 JSX 转换，它会自动从 React 包中导入必要的函数，因此你不再需要在每个使用 JSX 的文件中导入 `React`。该转换将 JSX 转换为对 `jsx` 或 `jsxs` 函数的直接调用。

下面是一个简单的 JSX 元素是如何转换的：

**编译前：**
```jsx
const element = <h1 className="greeting">Hello, world!</h1>;
```

**编译后：**
```javascript
import { jsx as _jsx } from 'react/jsx-runtime';

const element = _jsx('h1', { className: 'greeting', children: 'Hello, world!' });
```

这个编译步骤将你的声明式 JSX 转换成具体的 `ReactElement` 对象，React 可以使用这些对象来构建 DOM。

```d2
direction: down

"代码中的 JSX": {
  shape: document
  "const element = <MyComponent name='React' />"
}

"构建工具（例如 Babel）": {
  shape: hexagon
}

"JavaScript 函数调用": {
  shape: document
  "jsx(MyComponent, { name: 'React' })"
}

"React 运行时": {
  shape: package
  "ReactElement() 工厂"
}

"React 元素对象": {
  shape: stored_data
  "{ $$typeof: REACT_ELEMENT_TYPE, type: MyComponent, ... }"
}

"渲染后的 UI": {
   shape: rectangle
   "屏幕上实际显示的组件"
}

"代码中的 JSX" -> "构建工具（例如 Babel）": "转译"
"构建工具（例如 Babel）" -> "JavaScript 函数调用": "输出"
"JavaScript 函数调用" -> "React 运行时": "调用"
"React 运行时" -> "React 元素对象": "创建"
"React 元素对象" -> "渲染后的 UI": "被 React 用于渲染"
```

## JSX 运行时

React 提供了针对特定环境和模式（开发 vs. 生产）的不同 JSX 运行时。你的构建工具会根据你的配置自动选择正确的运行时。

| 运行时 | 用途 |
|---|---|
| `react/jsx-runtime` | 用于客户端应用程序的主要生产运行时。它为性能进行了优化。 |
| `react/jsx-dev-runtime` | 用于客户端应用程序的开发运行时。它包含额外的检查、验证和有用的警告。 |
| `react/jsx-runtime/server` | 专用于服务器环境的生产运行时，例如在使用 React Server Components 时。 |
| `react/jsx-dev-runtime/server`| 用于服务器环境的开发运行时，提供特定于服务器的警告和调试信息。 |

这些入口点导出了驱动 JSX 的核心函数。

## 核心 JSX 函数

虽然你通常不会直接调用这些函数，但理解它们有助于阐明 JSX 的工作原理。

<x-cards data-columns="3">
  <x-card data-title="jsx()" data-icon="lucide:code">
    用于创建一个带单个子元素或动态子元素的 React 元素。编译器将其用于像 `<div>{name}</div>` 这样的元素。
  </x-card>
  <x-card data-title="jsxs()" data-icon="lucide:codesandbox">
    一个用于创建具有多个静态子元素的优化版本。编译器将其用于像 `<div><p>First</p><p>Second</p></div>` 这样的元素，并将子元素数组标记为静态以进行潜在的优化。
  </x-card>
  <x-card data-title="jsxDEV()" data-icon="lucide:bug">
    仅用于开发环境的版本。它会验证 props，检查数组中缺失的 `key` prop，并提供其他必要的警告以在开发过程中捕获潜在的错误。
  </x-card>
</x-cards>

## 特殊 Props：key 和 ref

某些 props 在 JSX 中具有特殊含义，并由 React 进行特殊处理。

### key Prop

`key` prop 是一个特殊的字符串属性，你在创建元素列表时需要包含它。Key 帮助 React 识别哪些项已更改、已添加或已删除，这对于高效更新和维护列表中的状态至关重要。

- **Key 在同级元素中必须是唯一的**。
- **Key 不会作为 prop** 传递给你的组件。如果你需要在组件中使用相同的值，必须将其作为不同的 prop 传递（例如，`<Profile key={user.id} id={user.id} />`）。

在开发模式下，尝试在组件内访问 `props.key` 会产生一个警告：

> `key` 不是一个 prop。尝试访问它将导致返回 `undefined`。如果你需要在子组件中访问相同的值，你应该将其作为不同的 prop 传递。

### ref Prop

`ref` prop 用于直接访问 DOM 元素或类组件实例。与大多数 props 不同，`ref` 由 React 处理，不会传递给组件的 props 对象。

在 React 19 中，`ref` 作为常规 prop 传递。然而，访问 `element.ref` 已被弃用，并会在开发模式下触发警告，以鼓励使用现代的基于 prop 的方法。

---

现在你已经了解了 JSX 在幕后是如何工作的，你可以开始了解如何使用它来构建可复用的 UI。请继续阅读[组件与 Props](./core-apis-components-and-props.md)指南以了解更多信息。