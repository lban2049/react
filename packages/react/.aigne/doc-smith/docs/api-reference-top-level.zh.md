# 顶层 API

本节提供了 `react` 包中可用的顶层函数的详细文档。这些 API 是创建组件、管理上下文和执行高级优化的基本构建块。虽然使用 JSX 和 Hooks 的现代工作流可能会将其中一些抽象掉，但理解它们是掌握 React 的关键。

有关函数组件中与状态和生命周期相关的 API，请参阅 [Hooks 文档](./api-reference-hooks.md)。有关内置组件（如 `<Fragment>` 和 `<Suspense>`），请参阅 [组件与类参考](./api-reference-components.md)。

---

## createElement()

创建并返回一个给定类型的新 React 元素。这是传统 JSX 转换中 JSX 编译成的基本函数。如果使用 JSX，通常不会直接调用 `createElement()`。

### 参数
<x-field data-name="type" data-type="string | function | class" data-required="true" data-desc="元素类型。可以是一个标签名称字符串（例如 'div'、'span'）、一个 React 组件类型（一个类或一个函数），或一个 React 片段类型。"></x-field>
<x-field data-name="config" data-type="object" data-required="false" data-desc="一个包含元素 props 的对象。它也可以包含 'key' 和 'ref' 等特殊属性。"></x-field>
<x-field data-name="...children" data-type="ReactNode" data-required="false" data-desc="可变数量的子参数。这些可以是其他 React 元素、字符串、数字或节点数组。"></x-field>

### 返回值
<x-field data-name="ReactElement" data-type="object" data-desc="一个 React 元素对象，它是对要渲染内容的轻量级描述。"></x-field>

### 示例

```javascript Example icon=logos:javascript
import React from 'react';

// JSX syntax
const elementWithJsx = <h1 className="greeting">Hello, world!</h1>;

// The equivalent call using React.createElement
const elementWithoutJsx = React.createElement(
  'h1',
  { className: 'greeting' },
  'Hello, world!'
);
```

---

## createContext()

创建一个上下文（Context）对象。当 React 渲染一个订阅此上下文对象的组件时，它将从树中最近的匹配 `Provider` 中读取当前的上下文值。

### 参数
<x-field data-name="defaultValue" data-type="any" data-required="true" data-desc="当上层树中没有匹配的 Provider 时，消费者（consumer）获取到的值。这有助于隔离测试组件。"></x-field>

### 返回值
<x-field data-name="Context" data-type="object" data-desc="一个具有两个属性的上下文对象：`Provider` 和 `Consumer`。大多数现代应用程序将使用 `useContext` Hook 而不是 `Consumer` 组件。">
  <x-field data-name="Provider" data-type="React.Component" data-desc="一个允许消费者订阅上下文变化的组件。"></x-field>
  <x-field data-name="Consumer" data-type="React.Component" data-desc="一个订阅上下文变化的组件。需要一个函数作为其子元素。"></x-field>
</x-field>

### 示例

```javascript ThemeContext.js icon=logos:javascript
import { createContext } from 'react';

export const ThemeContext = createContext('light');
```

```javascript App.js icon=logos:javascript
import { ThemeContext } from './ThemeContext';
import ThemedButton from './ThemedButton';

function App() {
  return (
    <ThemeContext.Provider value="dark">
      <ThemedButton />
    </ThemeContext.Provider>
  );
}
```

```javascript ThemedButton.js icon=logos:javascript
import { useContext } from 'react';
import { ThemeContext } from './ThemeContext';

function ThemedButton() {
  const theme = useContext(ThemeContext);
  return <button className={`theme-${theme}`}>I am a {theme} button</button>;
}
```

---

## forwardRef()

`forwardRef` 创建一个 React 组件，该组件将其接收到的 `ref` 属性转发给树中更深层的另一个组件。这对于将子组件的 DOM 节点暴露给其父组件以执行命令式操作（如管理焦点）特别有用。

### 参数
<x-field data-name="render" data-type="function" data-required="true" data-desc="一个渲染函数，它接收 `props` 作为第一个参数，`ref` 作为第二个参数。该函数应返回一个 React 节点。"></x-field>

### 返回值
<x-field data-name="ReactComponent" data-type="object" data-desc="一种可以被渲染的新的 React 组件类型。"></x-field>

### 示例

```javascript FancyButton.js icon=logos:javascript
import React, { forwardRef } from 'react';

const FancyButton = forwardRef((props, ref) => (
  <button ref={ref} className="FancyButton">
    {props.children}
  </button>
));

export default FancyButton;
```

```javascript App.js icon=logos:javascript
import React, { useRef, useEffect } from 'react';
import FancyButton from './FancyButton';

function App() {
  const buttonRef = useRef(null);

  useEffect(() => {
    // The ref gives us direct access to the <button> DOM element
    if (buttonRef.current) {
      buttonRef.current.focus();
    }
  }, []);

  return (
    <FancyButton ref={buttonRef}>
      Click me!
    </FancyButton>
  );
}
```

---

## lazy()

`lazy()` 允许你定义一个动态加载的组件（代码分割）。这有助于减少应用程序的初始包大小。`lazy` 组件应在 `Suspense` 组件内部渲染，`Suspense` 允许你指定一个加载指示器。

### 参数
<x-field data-name="factory" data-type="function" data-required="true" data-desc="一个必须调用动态 `import()` 的函数。该函数必须返回一个 Promise，该 Promise 解析为一个模块，该模块带有一个包含 React 组件的 `default` 导出。"></x-field>

### 返回值
<x-field data-name="LazyComponent" data-type="object" data-desc="一种特殊的 React 组件类型，你可以在你的组件树中渲染它。"></x-field>

### 示例

```javascript App.js icon=logos:javascript
import React, { Suspense, lazy } from 'react';

const OtherComponent = lazy(() => import('./OtherComponent'));

function App() {
  return (
    <div>
      <h1>My App</h1>
      <Suspense fallback={<div>Loading...</div>}>
        <OtherComponent />
      </Suspense>
    </div>
  );
}
```

---

## memo()

`memo()` 是一个高阶组件，用于记忆化（memoizes）一个已渲染的组件。这是 React 为函数组件提供的一种性能优化方式，如果其 props 浅层相等，则可防止重新渲染。

### 参数
<x-field data-name="Component" data-type="function" data-required="true" data-desc="要被记忆化的函数组件。"></x-field>
<x-field data-name="areEqual" data-type="function" data-required="false" data-desc="一个可选的自定义比较函数。它接收旧的 props 和新的 props。如果返回 true，则跳过更新。"></x-field>

### 返回值
<x-field data-name="MemoizedComponent" data-type="object" data-desc="一个新的、经过记忆化的 React 组件类型。"></x-field>

### 示例

```javascript MyComponent.js icon=logos:javascript
import React, { memo } from 'react';

const MyComponent = memo(function MyComponent(props) {
  /* renders only when props change */
  console.log('Rendering MyComponent');
  return <div>{props.text}</div>;
});

export default MyComponent;
```

---

## 其他顶层 API

<x-cards>
<x-card data-title="cloneElement()" data-icon="lucide:copy">
克隆并返回一个以某个元素为起点的新 React 元素。`config` 可以包含新的 props。最终的元素将拥有原始元素的 props，并与新的 props 合并。
</x-card>
<x-card data-title="createRef()" data-icon="lucide:mouse-pointer-square">
创建一个 ref 对象。它通常用于类组件中，以获取对 DOM 元素或组件实例的引用。在函数组件中，首选 `useRef` Hook。
</x-card>
<x-card data-title="isValidElement()" data-icon="lucide:check-square">
验证一个对象是否为 React 元素。如果该对象是有效的 React 元素，则返回 `true`，否则返回 `false`。可用于验证 `props.children`。
</x-card>
</x-cards>

### 后续步骤

既然你已经了解了这些顶层 API，你可能想更详细地学习如何使用 `props.children` 数据结构。请继续阅读下一节，了解 `React.Children` 工具。

<x-card data-title="下一步：React.Children" data-icon="lucide:arrow-right" data-href="/api-reference/children" data-cta="阅读更多">
探索用于映射、计数和操作子元素的工具。
</x-card>