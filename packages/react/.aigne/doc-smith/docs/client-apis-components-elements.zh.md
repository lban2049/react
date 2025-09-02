# 组件和元素

本节提供 React 内置组件以及用于创建和操作 React 元素的基本实用工具的全面文档。这些 API 对于定义客户端应用程序的结构和行为至关重要。

要深入了解组件如何管理其状态和副作用，请参阅[Hooks 参考](./client-apis-hooks.md)。要掌握组件和 JSX 背后的基本概念，请访问[核心概念](./core-concepts.md)部分。

## 基础组件类

React 提供了两个用于定义组件的主要基类：`Component` 和 `PureComponent`。它们用于构建基于类的组件。

### `Component`

`Component` 是 React 类组件的基类。使用此类别定义的组件维护其自身的状态和生命周期方法，从而可以对渲染行为进行精细控制。

#### `new Component(props, context, updater)`

`Component` 的构造函数使用给定的 `props`、`context` 和 `updater` 对象（用于处理状态更新）初始化新的组件实例。

#### `component.setState(partialState, callback)`

`setState` 是更新组件状态的主要方法。当状态更改时，React 会重新渲染组件。它可以与对象或函数一起调用。

将 `this.state` 视为不可变很重要。不能保证在调用 `setState` 后 `this.state` 会立即更新，因为更新可能会为了性能而被批量处理。

**参数**

| Name | Type | Description |
|---|---|---|
| `partialState` | `object` \| `function` | 一个包含要与当前状态合并的新状态的对象，或一个接收先前的状态和属性并返回一个要更新的状态变量对象的函数。 |
| `callback` | `?function` | 一个可选的回调函数，它将在状态更新完成后且组件已重新渲染后执行。 |

**示例**

```javascript
import React from 'react';

class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
  }

  incrementCount = () => {
    this.setState(prevState => ({
      count: prevState.count + 1
    }), () => {
      console.log('State updated to:', this.state.count);
    });
  };

  render() {
    return (
      <button onClick={this.incrementCount}>
        Count: {this.state.count}
      </button>
    );
  }
}

// Usage example (e.g., in a root component)
// function App() {
//   return <MyComponent />;
// }
// export default App;
```

此示例演示了如何使用带函数式更新的 `setState` 更新 `count` 状态，并在应用更新后记录新状态。

#### `component.forceUpdate(callback)`

`forceUpdate` 强制组件重新渲染，绕过 `shouldComponentUpdate`。此方法应谨慎使用，仅当您知道组件状态或属性的某些更深层方面已更改但未调用 `setState` 时才使用。

**参数**

| Name | Type | Description |
|---|---|---|
| `callback` | `?function` | 一个可选的回调函数，它将在组件更新完成后执行。 |

**示例**

```javascript
import React from 'react';

class DataDisplay extends React.Component {
  constructor(props) {
    super(props);
    this.data = { value: 'Initial' };
  }

  // Simulate direct mutation (generally discouraged)
  mutateDataAndForceUpdate = () => {
    this.data.value = 'Updated via forceUpdate at ' + new Date().toLocaleTimeString();
    this.forceUpdate(() => {
      console.log('Component forced updated. Displaying:', this.data.value);
    });
  };

  render() {
    return (
      <div>
        <p>Current Data: {this.data.value}</p>
        <button onClick={this.mutateDataAndForceUpdate}>
          Force Update
        </button>
      </div>
    );
  }
}

// Usage example
// function App() {
//   return <DataDisplay />;
// }
// export default App;
```

此示例展示了在组件实例属性被直接（且通常不鼓励）修改后使用 `forceUpdate`。`forceUpdate` 明确地触发了重新渲染。

### `PureComponent`

`PureComponent` 类似于 `Component`，但在其 `shouldComponentUpdate` 方法中包含了浅层属性和状态比较。这意味着 `PureComponent` 仅在其属性或状态发生浅层更改时才会重新渲染，通过避免对具有简单属性和状态的组件进行不必要的重新渲染，可能提供性能优势。

**示例**

```javascript
import React from 'react';

class OptimizedDisplay extends React.PureComponent {
  render() {
    console.log('OptimizedDisplay rendered');
    return (
      <div>
        <p>Value: {this.props.value}</p>
      </div>
    );
  }
}

// When parent re-renders but props.value doesn't shallowly change,
// OptimizedDisplay will not re-render.
// function App() {
//   const [data, setData] = React.useState({ value: 'hello' });
//   React.useEffect(() => {
//     const interval = setInterval(() => {
//       // This will cause App to re-render, but OptimizedDisplay will not
//       // re-render if data.value is shallowly equal.
//       setData(prev => ({ ...prev })); 
//     }, 1000);
//     return () => clearInterval(interval);
//   }, []);
//
//   return <OptimizedDisplay value={data.value} />;
// }
// export default App;
```

在此示例中，由于 `PureComponent` 的浅层比较，`OptimizedDisplay` 仅在其 `value` 属性更改时才会重新渲染。

## 核心元素工厂

React 提供了用于创建和操作元素的函数，它们是 UI 的构建块。

### `createElement(type, props, ...children)`

`createElement` 是创建 React 元素的核心函数。虽然它通常被 JSX 语法抽象，但可以直接调用它来以编程方式构造元素。元素是您想要渲染内容的轻量级描述。

**参数**

| Name | Type | Description |
|---|---|---|
| `type` | `string` \| `function` \| `object` | 元素的类型。这可以是 HTML 标签名字符串（例如，`'div'`、`'p'`），React 组件类，React 函数组件，或 React 符号（例如，`React.Fragment`）。 |
| `props` | `?object` | 一个包含要传递给元素的属性的对象。这可以包括 `key` 和 `ref` 特殊属性。 |
| `...children` | `any` | 要在创建的元素内部渲染的零个或多个子元素或值。 |

**返回值**

| Name | Type | Description |
|---|---|---|
| `element` | `ReactElement` | 一个新的 React 元素对象。 |

**示例**

```javascript
import React from 'react';

const MyGreeting = ({ name }) => React.createElement('h1', null, `Hello, ${name}!`);

// Creating a simple div element
const divElement = React.createElement(
  'div',
  { className: 'container' },
  'This is some text',
  React.createElement('p', null, 'And a paragraph.')
);

// Creating a component element
const greetingElement = React.createElement(MyGreeting, { name: 'World' });

// Example of how these elements might be used within a component render method:
// function App() {
//   return (
//     <div>
//       {divElement}
//       {greetingElement}
//     </div>
//   );
// }
// export default App;
```

此示例演示了如何使用 `createElement` 创建带有子元素的 `div` 元素以及从函数组件创建元素。

### `cloneElement(element, props, ...children)`

`cloneElement` 用于以现有元素为起点创建新的 React 元素。它允许您覆盖或添加新的属性、`key` 和 `ref` 到克隆元素，同时保留原始元素的 `type` 和 `_owner`。

**参数**

| Name | Type | Description |
|---|---|---|
| `element` | `ReactElement` | 要克隆的现有 React 元素。 |
| `props` | `?object` | 一个包含要与原始元素属性合并的新属性的对象。新属性将覆盖现有属性。 |
| `...children` | `any` | 要在克隆元素内部渲染的新子元素。这些将替换原始子元素。 |

**返回值**

| Name | Type | Description |
|---|---|---|
| `clonedElement` | `ReactElement` | 一个新的 React 元素，是原始元素的克隆，带有更新的属性和子元素。 |

**示例**

```javascript
import React from 'react';

function Greeting({ message, style }) {
  return <h1 style={style}>{message}</h1>;
}

const originalGreeting = <Greeting message="Hello" style={{ color: 'blue' }} />;

// Clone the original greeting, changing the message and adding a new style
const clonedGreeting = React.cloneElement(
  originalGreeting,
  { message: 'Greetings from React!', style: { color: 'green', fontSize: '24px' } },
  // Children can also be passed here if the original element accepted them
);

// Example of usage:
// function App() {
//   return (
//     <div>
//       {originalGreeting}
//       {clonedGreeting}
//     </div>
//   );
// }
// export default App;
```

此示例克隆了一个现有的 `Greeting` 元素，更改了其 `message` 属性并添加了一个新样式。

### `isValidElement(object)`

`isValidElement` 检查给定对象是否是有效的 React 元素。这对于类型检查很有用，尤其是在处理子元素或动态创建的内容时。

**参数**

| Name | Type | Description |
|---|---|---|
| `object` | `any` | 要检查的对象。 |

**返回值**

| Name | Type | Description |
|---|---|---|
| `boolean` | `boolean` | 如果对象是 React 元素，则为 `true`；否则为 `false`。 |

**示例**

```javascript
import React from 'react';

const myElement = <div>Hello</div>;
const myObject = { value: 123 };

console.log(React.isValidElement(myElement)); // true
console.log(React.isValidElement(myObject));  // false
console.log(React.isValidElement(null));     // false
console.log(React.isValidElement('string')); // false

// Example of usage within a component:
// function ChildChecker({ children }) {
//   return (
//     <div>
//       {React.isValidElement(children) ? (
//         <p>Child is a valid React element.</p>
//       ) : (
//         <p>Child is NOT a valid React element.</p>
//       )}
//     </div>
//   );
// }
// function App() {
//   return (
//     <div>
//       <ChildChecker><span>Test</span></ChildChecker>
//       <ChildChecker>{123}</ChildChecker>
//     </div>
//   );
// }
// export default App;
```

此示例演示了 `isValidElement` 如何区分 React 元素与其他 JavaScript 对象或基本类型。

## 内置特殊组件

React 包含几个特殊组件，它们提供特定行为或开发时实用工具。

### `Fragment`

`Fragment` 允许您将子元素列表分组，而无需向 DOM 添加额外的节点。当您需要从组件返回多个元素但又不想将它们包装在不必要的 `div` 中时，这会很有用。

**示例**

```javascript
import React, { Fragment } from 'react';

function ListItem() {
  return (
    <Fragment>
      <td>Item 1</td>
      <td>Item 2</td>
    </Fragment>
  );
}

// Using shorthand syntax:
function AnotherListItem() {
  return (
    <>
      <td>Item A</td>
      <td>Item B</td>
    </>
  );
}

// Example usage within a table:
// function App() {
//   return (
//     <table>
//       <tbody>
//         <tr>
//           <ListItem />
//         </tr>
//         <tr>
//           <AnotherListItem />
//         </tr>
//       </tbody>
//     </table>
//   );
// }
// export default App;
```

此示例展示了如何使用 `Fragment`（或其简写 `<></>`）来返回多个 `td` 元素，而无需将它们包装在额外的 DOM 节点中，这对于有效的 HTML 表格结构至关重要。

### `Profiler`

`Profiler` 是一个用于测量 React 树渲染性能的组件。它收集有关 React 应用程序渲染频率、其中组件渲染内容以及渲染所需时间的时间信息。它可以放置在 React 树中的任何位置，以测量其后代的性能。

**参数**

| Name | Type | Description |
|---|---|---|
| `id` | `string` | 一个字符串，用于标识您正在分析的 UI 部分。 |
| `onRender` | `function` | 一个回调函数，每当分析树中的组件“提交”更新时，React 都会调用它。 |

**示例**

```javascript
import React, { Profiler } from 'react';

function onRenderCallback(
  id, // the "id" prop of the Profiler tree that has just committed
  phase, // either "mount" (if the tree just mounted) or "update" (if it re-rendered)
  actualDuration, // time spent rendering the committed update
  baseDuration, // estimated time to render the entire subtree without memoization
  startTime, // when React began rendering this update
  commitTime, // when React committed this update
  interactions // Set of interactions belonging to this update
) {
  // Aggregate or log render timings...
  console.log(`Profiler ${id} rendered: phase=${phase}, actualDuration=${actualDuration.toFixed(2)}ms`);
}

// Example usage:
// function App() {
//   return (
//     <Profiler id="ApplicationRoot" onRender={onRenderCallback}>
//       <MyComponent />
//       <AnotherComponent />
//     </Profiler>
//   );
// }
// export default App;
```

此示例演示了如何使用带 `onRender` 回调的 `Profiler` 来记录应用程序某个部分的性能指标。

### `StrictMode`

`StrictMode` 是一种在开发过程中突出 React 应用程序中潜在问题的工具。与 `Fragment` 类似，`StrictMode` 不会渲染任何可见的 UI。它为其后代激活额外的检查和警告，这些检查和警告仅在开发模式下运行，不会影响生产构建。

**示例**

```javascript
import React from 'react';

function App() {
  return (
    <React.StrictMode>
      <MyLegacyComponent />
    </React.StrictMode>
  );
}

// MyLegacyComponent might use deprecated lifecycle methods or have other potential issues.
// StrictMode will warn about these in the console during development.
```

此示例展示了如何使用 `StrictMode` 包装组件，以启用仅在开发期间的警告和检查。

### `Suspense`

`Suspense` 允许您的组件在渲染之前“等待”某些内容，例如使用 `React.lazy` 延迟加载的代码或从网络请求加载的数据。它提供了一种优雅地处理 UI 中加载状态的方法。

**参数**

| Name | Type | Description |
|---|---|---|
| `fallback` | `React.Node` | 一个 React 元素，它将在子元素加载时渲染。这可以是任何 React 节点，例如加载旋转器或占位符 UI。 |

**示例**

```javascript
import React, { Suspense, lazy } from 'react';

const LazyComponent = lazy(() => import('./LazyComponent'));

function App() {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <LazyComponent />
    </Suspense>
  );
}

// LazyComponent.js (separate file)
// export default function LazyComponent() {
//   return <h2>I'm a lazily loaded component!</h2>;
// }
```

此示例演示了如何使用 `Suspense` 在 `LazyComponent`（通过 `React.lazy` 加载）正在获取时显示加载指示器。

### `unstable_SuspenseList`

`unstable_SuspenseList` 是一个实验性组件，它协调列表中多个 `Suspense` 和 `unstable_LegacyHidden` 组件如何向用户显示。它允许您定义它们出现的顺序或组，从而改善加载多个项目的用户体验。

**参数**

| Name | Type | Description |
|---|---|---|
| `revealOrder` | `string` | 定义列表中项目应显示的顺序。可以是 `'forwards'`（向前）、`'backwards'`（向后）或 `'together'`（同时）。 |
| `tail` | `string` | 定义是显示所有回退内容（`'collapsed'`）还是仅显示下一个回退内容（`'hidden'`）。可以是 `'collapsed'` 或 `'hidden'`。 |
| `children` | `React.Node` | 多个 `Suspense` 组件或其他可以挂起的组件。 |

**示例**

```javascript
import React, { Suspense, unstable_SuspenseList, lazy } from 'react';

const ComponentA = lazy(() => import('./ComponentA'));
const ComponentB = lazy(() => import('./ComponentB'));
const ComponentC = lazy(() => import('./ComponentC'));

function Dashboard() {
  return (
    <unstable_SuspenseList revealOrder="forwards" tail="collapsed">
      <Suspense fallback={<div>Loading A...</div>}>
        <ComponentA />
      </Suspense>
      <Suspense fallback={<div>Loading B...</div>}>
        <ComponentB />
      </Suspense>
      <Suspense fallback={<div>Loading C...</div>}>
        <ComponentC />
      </Suspense>
    </unstable_SuspenseList>
  );
}
```

此示例演示了 `unstable_SuspenseList` 如何按向前顺序显示 `ComponentA`、`ComponentB` 和 `ComponentC`，并在项目加载时回退内容折叠。

### `unstable_LegacyHidden`

`unstable_LegacyHidden` 是一个实验性组件，用于在不卸载内容的情况下向用户隐藏内容，从而保留其状态和 DOM。这对于屏幕外导航或选项卡视图非常有用，在这些情况下，您希望保持非活动选项卡处于挂载状态，以便更快地重新出现。

**参数**

| Name | Type | Description |
|---|---|---|
| `mode` | `string` | 指定内容的可见性模式。可以是 `'hidden'`（隐藏内容）或 `'visible'`（显示内容）。 |
| `children` | `React.Node` | 要条件性隐藏或显示的内容。 |

**示例**

```javascript
import React, { useState, unstable_LegacyHidden } from 'react';

function TabComponent() {
  const [activeTab, setActiveTab] = useState('tab1');

  return (
    <div>
      <button onClick={() => setActiveTab('tab1')}>Tab 1</button>
      <button onClick={() => setActiveTab('tab2')}>Tab 2</button>

      <unstable_LegacyHidden mode={activeTab === 'tab1' ? 'visible' : 'hidden'}>
        <div>Content for Tab 1</div>
      </unstable_LegacyHidden>
      <unstable_LegacyHidden mode={activeTab === 'tab2' ? 'visible' : 'hidden'}>
        <div>Content for Tab 2</div>
      </unstable_LegacyHidden>
    </div>
  );
}
```

此示例使用 `unstable_LegacyHidden` 在选项卡内容之间切换，同时保持非活动选项卡的组件已挂载但已隐藏。

### `unstable_Activity`

`unstable_Activity` 是一个实验性组件，与跟踪 React 树中的用户交互和活动状态相关。它通常用于内部性能监控或分析，尤其是在 UI 部分的活动状态可能动态变化的并发渲染场景中。

**参数**

| Name | Type | Description |
|---|---|---|
| `mode` | `string` | 包装内容的活动模式，通常为 `'visible'`（可见）或 `'hidden'`（隐藏）。 |
| `children` | `React.Node` | 正在跟踪其活动状态的内容。 |

**示例**

```javascript
import React, { unstable_Activity } from 'react';

function UserActivityTracker({ children }) {
  // In a real application, this component might integrate with an analytics system
  // or dispatch events based on the visibility/activity of its children.
  const onVisibilityChange = (isVisible) => {
    console.log(`Content is now ${isVisible ? 'visible' : 'hidden'}`);
  };

  return (
    <unstable_Activity mode="visible" /* mode could be dynamically set */>
      {children}
    </unstable_Activity>
  );
}
```

此概念性示例演示了 `unstable_Activity` 如何包装组件以跟踪其可见性或活动状态。

### `unstable_Scope`

`unstable_Scope` 是一个实验性功能，旨在 React 树中创建独立的渲染范围。这可能与内部优化或特定渲染策略相关，在这些情况下，独立的渲染环境是有益的。

**示例**

```javascript
import React, { unstable_Scope } from 'react';

function MyScopedComponent() {
  return (
    <unstable_Scope>
      {/* Components rendered within this scope might benefit from specific rendering behaviors or optimizations. */}
      <p>Content inside a custom scope.</p>
    </unstable_Scope>
  );
}
```

此示例展示了 `unstable_Scope` 的基本用法，用于定义独立的渲染边界。

### `unstable_TracingMarker`

`unstable_TracingMarker` 是一个实验性组件，允许开发人员标记其 UI 的特定部分以进行性能跟踪和调试。它对于理解过渡性能和识别标记区域内的渲染瓶颈特别有用。

**参数**

| Name | Type | Description |
|---|---|---|
| `name` | `string` | 一个用于跟踪标记的描述性名称，在性能工具中使用。 |
| `onAllReady` | `?function` | 一个可选的回调函数，当标记部分内的所有内容完成加载或渲染时调用。 |
| `children` | `React.Node` | 要跟踪的 UI 部分。 |

**示例**

```javascript
import React, { unstable_TracingMarker } from 'react';

function ProductPage() {
  const handleAllReady = () => {
    console.log('Product page content fully loaded and rendered.');
  };

  return (
    <unstable_TracingMarker name="ProductPageLoad" onAllReady={handleAllReady}>
      {/* Components related to product details and reviews */}
      <ProductDetails />
      <ProductReviews />
    </unstable_TracingMarker>
  );
}
```

此示例演示了如何使用 `unstable_TracingMarker` 来测量和记录产品页面内容何时完全准备就绪。

### `unstable_ViewTransition`

`unstable_ViewTransition` 是一个实验性组件，旨在与浏览器的视图过渡 API 集成，从而在不同的 UI 状态之间实现更流畅、更具视觉吸引力的过渡，而无需复杂的手动动画。它允许浏览器处理不同视图之间的交叉淡入淡出或自定义动画。

**参数**

| Name | Type | Description |
|---|---|---|
| `children` | `React.Node` | 将参与视图过渡的内容。此内容将在过渡期间被捕获并动画化。 |

**示例**

```javascript
import React, { unstable_ViewTransition, useState } from 'react';

function Gallery() {
  const [showDetails, setShowDetails] = useState(false);

  return (
    <unstable_ViewTransition>
      {showDetails ? (
        <ProductDetails />
      ) : (
        <ThumbnailGrid />
      )}
      <button onClick={() => setShowDetails(!showDetails)}>Toggle Details</button>
    </unstable_ViewTransition>
  );
}

// ProductDetails and ThumbnailGrid would be your regular React components.
```

此示例展示了 `unstable_ViewTransition` 如何包装条件渲染的组件，以便在其状态更改时实现平滑的视觉过渡。

## 实用函数

除了组件之外，React 还提供了几个实用函数，可帮助处理与元素和上下文相关的常见任务。

### `createRef()`

`createRef` 返回一个 ref 对象，可以通过 `ref` 属性将其附加到 React 元素。这提供了一种访问 `render` 方法中创建的 DOM 节点或 React 组件的方法，允许直接交互或命令式操作。

**参数**

无。

**返回值**

| Name | Type | Description |
|---|---|---|
| `refObject` | `RefObject` | 一个具有单个可变属性 `current` 的对象，初始化为 `null`。此 `current` 属性将在组件挂载后保存 DOM 元素或组件实例。 |

**示例**

```javascript
import React, { createRef, Component } from 'react';

class MyForm extends Component {
  constructor(props) {
    super(props);
    this.textInput = createRef();
  }

  focusTextInput = () => {
    // Directly access the DOM node via the ref to focus the input
    if (this.textInput.current) {
      this.textInput.current.focus();
    }
  };

  render() {
    return (
      <div>
        <input type="text" ref={this.textInput} />
        <button onClick={this.focusTextInput}>Focus the text input</button>
      </div>
    );
  }
}
```

此示例演示了如何使用 `createRef` 创建一个 ref 并将其附加到输入元素以编程方式聚焦它。

### `createContext(defaultValue)`

`createContext` 创建一个 Context 对象。当 React 渲染订阅此 Context 对象的组件时，它将从组件树中其上方最接近的匹配 `Provider` 读取当前 Context 值。这允许数据在不手动传递每一层属性的情况下深度传递到组件树中。

**参数**

| Name | Type | Description |
|---|---|---|
| `defaultValue` | `any` | 上下文的默认值。当组件在组件树中没有匹配的 Provider 或在没有 Provider 的情况下渲染时，将使用此值。 |

**返回值**

| Name | Type | Description |
|---|---|---|
| `context` | `ReactContext` | 一个 Context 对象。此对象附带一个 `Provider` React 组件，允许消费组件订阅上下文更改，以及一个 `Consumer` 组件（或通常与 `useContext` 钩子一起使用）。 |

**示例**

```javascript
import React, { createContext, useContext } from 'react';

// 1. Create a Context object
const ThemeContext = createContext('light'); // 'light' is the default value

// 2. Provider component to supply the context value
function ThemeProvider({ children }) {
  const theme = 'dark'; // In a real application, this would typically come from component state or props
  return (
    <ThemeContext.Provider value={theme}>
      {children}
    </ThemeContext.Provider>
  );
}

// 3. Consumer component to read the context value
function ThemedButton() {
  const theme = useContext(ThemeContext); // Using the useContext hook to read context
  return (
    <button style={{ background: theme === 'dark' ? 'black' : 'white', color: theme === 'dark' ? 'white' : 'black' }}>
      My themed button
    </button>
  );
}

// Example usage within your application:
// function App() {
//   return (
//     <ThemeProvider>
//       <ThemedButton />
//     </ThemeProvider>
//   );
// }
// export default App;
```

此示例说明了 `createContext` 的基本用法，以建立一个可以由 `ThemeProvider` 提供并由 `ThemedButton` 消费的主题。

### `lazy(ctor)`

`lazy` 允许您定义一个动态（惰性）加载的组件。这有助于通过仅在实际渲染时才加载必要的组件来减小应用程序的初始包大小。惰性组件必须始终在 `Suspense` 组件内部渲染。

**参数**

| Name | Type | Description |
|---|---|---|
| `ctor` | `() => Thenable<{default: T, ...}>` | 一个返回 Promise（或 thenable）的函数，该 Promise 解析为模块对象。模块对象必须具有一个 `default` 导出，该导出是一个 React 组件。 |

**返回值**

| Name | Type | Description |
|---|---|---|
| `LazyComponent` | `LazyComponent<T, Payload<T>>` | 一个 React 组件类型，它将在渲染时惰性加载给定组件。 |

**示例**

```javascript
import React, { Suspense, lazy } from 'react';

// Define a lazy-loaded component. 'DetailComponent.js' would contain the actual component export.
const LazyLoadedComponent = lazy(() => import('./DetailComponent'));

function App() {
  const [showDetail, setShowDetail] = React.useState(false);

  return (
    <div>
      <button onClick={() => setShowDetail(!showDetail)}>
        Toggle Detail Component
      </button>
      {showDetail && (
        <Suspense fallback={<div>Loading Detail...</div>}>
          <LazyLoadedComponent />
        </Suspense>
      )}
    </div>
  );
}

// Example of DetailComponent.js (in a separate file)
// export default function DetailComponent() {
//   return <p>This content was loaded on demand!</p>;
// }
```

此示例演示了如何使用 `lazy` 仅在 `showDetail` 为 true 时加载 `DetailComponent`，并在加载时提供 `Suspense` 回退。

### `forwardRef(render)`

`forwardRef` 允许您的组件接收 `ref` 属性并将其转发给子组件，特别是转发给它渲染的 DOM 节点或类组件实例。当您希望父组件命令式地访问其子组件的底层 DOM 元素或组件实例时，这很有用。

**参数**

| Name | Type | Description |
|---|---|---|
| `render` | `(props, ref) => React.Node` | 一个渲染函数，它接收 `props` 和 `ref` 作为其两个参数，并返回一个 React 节点（例如，一个 JSX 元素）。 |

**返回值**

| Name | Type | Description |
|---|---|---|
| `elementType` | `object` | 一个 React 组件类型，它可以接收 `ref` 属性并将其转发。 |

**示例**

```javascript
import React, { forwardRef, useRef } from 'react';

// MyInput is a functional component that forwards its ref to the native input element.
const MyInput = forwardRef((props, ref) => (
  <input type="text" ref={ref} {...props} />
));

function App() {
  const inputRef = useRef(null); // Create a ref using the useRef hook

  const handleClick = () => {
    // When the button is clicked, focus the input element via its ref.
    if (inputRef.current) {
      inputRef.current.focus();
    }
  };

  return (
    <div>
      <MyInput ref={inputRef} placeholder="Enter text" />
      <button onClick={handleClick}>Focus Input</button>
    </div>
  );
}
```

此示例展示了 `forwardRef` 如何用于创建 `MyInput`，`MyInput` 可以从 `App` 接收一个 ref 并将其传递给实际的 `input` DOM 元素，从而允许 `App` 聚焦该输入。

### `memo(type, compare?)`

`memo` 是一个高阶组件（一个接受组件并返回新组件的函数），它记忆函数组件的渲染。如果组件的属性与之前的渲染相同（默认基于浅层比较或自定义 `compare` 函数），React 会跳过渲染该组件并重用上次渲染的结果。这可以通过防止不必要的组件重新渲染来提供显著的性能优化，这些组件接收不变的属性。

**参数**

| Name | Type | Description |
|---|---|---|
| `type` | `React$ElementType` | 要记忆的函数组件。`memo` 仅适用于函数组件。 |
| `compare` | `?(oldProps: Props, newProps: Props) => boolean` | 一个可选的比较函数。如果提供，React 将使用此函数比较 `oldProps` 和 `newProps`。如果返回 `true`，则跳过更新；如果返回 `false`，则组件将重新渲染。如果省略，React 将对所有属性执行浅层比较。 |

**返回值**

| Name | Type | Description |
|---|---|---|
| `elementType` | `object` | 一个记忆化的 React 组件类型。此新组件的行为与原始 `type` 类似，但包含记忆化优化。 |

**示例**

```javascript
import React, { memo, useState } from 'react';

const MyMemoizedComponent = memo(function MyComponent({ value }) {
  console.log('MyMemoizedComponent rendered'); // This will only log when 'value' actually changes
  return <p>Value: {value}</p>;
});

function App() {
  const [count, setCount] = useState(0);
  const [text, setText] = useState('hello');

  // MyMemoizedComponent will only re-render when its 'value' prop changes.
  // Changes to 'count' will cause App to re-render, but MyMemoizedComponent will not
  // re-render because its 'value' prop (text) has not changed.
  return (
    <div>
      <button onClick={() => setCount(count + 1)}>Increment Count: {count}</button>
      <input value={text} onChange={e => setText(e.target.value)} />
      <MyMemoizedComponent value={text} />
    </div>
  );
}
```

此示例演示了只要 `MyMemoizedComponent` 的 `value` 属性保持浅层相等，`memo` 如何防止 `MyMemoizedComponent` 在其父级 `App` 中的 `count` 状态更改时重新渲染。

---

本节介绍了 React 的基本组件类、核心元素创建和操作实用工具，以及用于特定行为（如性能分析或处理加载状态）的特殊内置组件。理解这些 API 对于构建健壮高效的客户端 React 应用程序至关重要。接下来，在[实用工具](./client-apis-utilities.md)部分探索其他用于管理子元素和访问 React 版本信息的通用函数。