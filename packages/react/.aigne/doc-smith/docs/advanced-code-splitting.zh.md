# 使用 `lazy` 和 `Suspense` 进行代码分割

代码分割是优化 Web 应用性能的一项强大技术。它涉及将代码分割成更小的代码块，这些代码块可以按需加载，而不是在用户首次访问页面时下载整个应用程序包。这会加快初始加载时间，特别是对于大型应用程序。

React 提供了使用 `React.lazy` 和 `React.Suspense` 为组件实现代码分割的简单而强大的方法。这种组合允许你渲染动态导入的组件，就像渲染常规的静态导入组件一样，同时在组件准备就绪前显示一个备用 UI（例如加载指示器）。

## 核心概念

### `React.lazy`

`React.lazy` 函数允许你定义一个动态加载的组件。它接受一个函数作为参数，该函数必须调用动态 `import()`。这个 `import()` 调用会返回一个 `Promise`，该 `Promise` 会解析为一个模块，该模块带有一个包含 React 组件的 `default` 导出。

**语法**

```javascript icon=logos:react
import React, { lazy } from 'react';

const MyLazyComponent = lazy(() => import('./MyLazyComponent'));
```

在底层，`React.lazy` 会创建一个特殊的 `LazyComponent` 对象。该对象有一个内部状态，用于跟踪动态导入是 `Pending`、`Resolved` 还是 `Rejected`。当 React 首次尝试渲染此组件时，它会触发动态导入并“暂停”渲染，直到 Promise 解析完成。

### `React.Suspense`

懒加载组件只能在 `React.Suspense` 组件边界内渲染。`Suspense` 允许你指定一个加载指示器（即 `fallback`），在懒加载组件被获取和加载时显示。

**语法**

```javascript icon=logos:react
import React, { Suspense } from 'react';

const MyLazyComponent = React.lazy(() => import('./MyLazyComponent'));

function App() {
  return (
    <div>
      <h1>My Application</h1>
      <Suspense fallback={<div>Loading...</div>}>
        <MyLazyComponent />
      </Suspense>
    </div>
  );
}
```

在此示例中，`<div>Loading...</div>` 将会替代 `MyLazyComponent` 进行渲染，直到 `MyLazyComponent.js` 的代码下载完毕并准备好显示。

## 工作原理：加载流程

`lazy` 和 `Suspense` 之间的交互为按需加载创造了无缝的用户体验。下图说明了该过程：

```d2 懒加载过程
direction: down

User: { 
  shape: c4-person 
}

React-App: {
  label: "React 应用"
  shape: rectangle

  Suspense-Boundary: {
    label: "<Suspense>"
    shape: rectangle

    Lazy-Component: {
      label: "<LazyComponent>"
      shape: rectangle
    }
  }
}

Server: {
  label: "Web 服务器"
  shape: cylinder
}

User -> React-App.Suspense-Boundary.Lazy-Component: "1. 触发懒加载组件的渲染"
React-App.Suspense-Boundary.Lazy-Component -> Server: "2. 启动动态 import() 以获取组件代码块"
React-App.Suspense-Boundary -> User: "3. 暂停渲染并显示备用 UI（例如，加载指示器）"
Server -> React-App.Suspense-Boundary.Lazy-Component: "4. 组件代码下载完成"
React-App.Suspense-Boundary.Lazy-Component -> React-App.Suspense-Boundary.Lazy-Component: "5. Promise 解析，组件就绪"
React-App.Suspense-Boundary.Lazy-Component -> User: "6. React 渲染实际组件，替换备用 UI"

```

## API 参考：`lazy()`

`lazy` 函数是创建代码分割组件的主要工具。

### `lazy(ctor)`

-   **参数**
    -   `ctor`: 一个返回 `Promise` 的函数。该函数仅在组件首次渲染时执行。它返回的 Promise 应解析为一个模块对象，该对象具有包含 React 组件的 `default` 导出。
-   **返回值**
    -   一个可以在组件树中渲染的 React 组件。加载期间，它会暂停并触发最近的 `<Suspense>` 边界的 fallback。

#### 内部结构

`lazy` 返回的对象是一种特殊类型，具有以下关键属性（定义于 React 源码中）：

| Property | Description |
|---|---|
| `$$typeof` | 一个符号 (`REACT_LAZY_TYPE`)，用于将其标识为懒加载组件。 |
| `_payload` | 一个持有加载状态 (`_status`) 和结果 (`_result`) 的对象。状态可以是 `Uninitialized`、`Pending`、`Resolved` 或 `Rejected`。 |
| `_init` | 一个函数 (`lazyInitializer`)，React 调用它来启动加载过程并处理 Promise 的生命周期。 |

## 常见用例

### 基于路由的代码分割

引入代码分割最有效的地方之一是在路由层面。通过懒加载每个页面的组件，可以确保用户只下载他们正在访问的特定页面的代码。

```javascript 基于路由的分割示例 icon=logos:react
import React, { Suspense, lazy } from 'react';
import { BrowserRouter as Router, Routes, Route } from 'react-router-dom';

const Home = lazy(() => import('./routes/Home'));
const About = lazy(() => import('./routes/About'));

const App = () => (
  <Router>
    <Suspense fallback={<div>Loading page...</div>}>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
      </Routes>
    </Suspense>
  </Router>
);
```

### 条件化组件加载

你还可以懒加载那些仅根据用户交互或其他条件才渲染的组件。这对于那些在初始渲染时不需要的重型组件（如模态框、复杂图表或特殊编辑器）非常有用。

```javascript 条件化组件示例 icon=logos:react
import React, { useState, Suspense, lazy } from 'react';

const HeavyChartComponent = lazy(() => import('./HeavyChartComponent'));

function Dashboard() {
  const [showChart, setShowChart] = useState(false);

  return (
    <div>
      <button onClick={() => setShowChart(true)}>Show Chart</button>
      {showChart && (
        <Suspense fallback={<div>Loading chart...</div>}>
          <HeavyChartComponent />
        </Suspense>
      )}
    </div>
  );
}
```

通过结合使用 `React.lazy` 和 `Suspense`，你可以显著提升应用的加载性能并提供更好的用户体验。

优化组件加载后，你可能希望探索如何平滑地管理复杂的 UI 更新。在 [Transitions](./advanced-transitions.md) 部分了解更多信息。