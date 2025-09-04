# 使用 `lazy` 和 `Suspense` 进行代码分割

代码分割是一种用于提升 Web 应用性能的技术，它将一个大的代码包分割成多个较小的代码块，这些代码块可以按需加载。这可以显著减少应用的初始加载时间，因为用户只需下载初始路由所需的代码。

React 提供了两个核心功能，可以轻松实现代码分割：`React.lazy` 用于定义应动态加载的组件，`React.Suspense` 用于指定这些组件在获取过程中的加载状态。

## `React.lazy()`

`React.lazy` 函数允许你将一个动态导入的组件像普通组件一样进行渲染。它接受一个函数作为参数，该函数必须调用动态的 `import()`。这个 `import()` 必须返回一个 `Promise`，该 `Promise` 会解析为一个带有 `default` 导出的模块，该导出包含一个 React 组件。

### 基本用法

在代码分割之前，你可能会静态导入一个组件：

```javascript
import MyComponent from './MyComponent';

const App = () => (
  <div>
    <MyComponent />
  </div>
);
```

使用 `React.lazy`，你可以推迟加载该组件的代码，直到它实际被渲染时：

```javascript
import React, { Suspense } from 'react';

const MyComponent = React.lazy(() => import('./MyComponent'));

const App = () => (
  <div>
    <Suspense fallback={<div>Loading...</div>}>
      <MyComponent />
    </Suspense>
  </div>
);
```

`lazy` 函数返回一个特殊的 `LazyComponent` 对象。该对象有一个值为 `REACT_LAZY_TYPE` 的 `$$typeof` 属性，并包含一个带有加载状态和结果的 `_payload`，以及一个 React 用来解析组件的 `_init` 函数 (`lazyInitializer`)。

## `React.Suspense`

使用 `React.lazy` 定义的组件必须在 `Suspense` 组件内部进行渲染。`Suspense` 允许你指定一个加载指示器（一个后备 UI），在懒加载组件加载期间显示。

The `fallback` 属性接受任何你希望在等待组件加载时渲染的 React 元素。

```javascript
<Suspense fallback={<p>Please wait, the component is loading.</p>}>
  <LazyLoadedComponent />
</Suspense>
```

你可以在组件树的高层放置一个 `Suspense` 组件来处理多个懒加载组件的加载状态，也可以为单个懒加载组件进行包裹以实现更精细的控制。

## 内部工作原理

当 React 首次尝试渲染 `LazyComponent` 时，会触发 `lazyInitializer` 函数。这个过程涉及几个内部状态：

| State | Value | Description |
|---|---|---|
| `Uninitialized` | -1 | 动态导入被调用之前的初始状态。 |
| `Pending` | 0 | 动态 `import()` 已被调用，且 promise 正在处理中。 |
| `Resolved` | 1 | Promise 成功解析，组件模块可用。 |
| `Rejected` | 2 | Promise 被拒绝，表示加载出错。 |

下图展示了使用 `Suspense` 的懒加载组件的生命周期。

```d2
direction: down

App-Render: {
  label: "App 渲染"
  shape: rectangle
}

Suspense-Boundary: {
  label: "<Suspense> 边界"
  shape: package

  Lazy-Component-Render: {
    label: "<LazyComponent> 被渲染"
    shape: rectangle
  }

  Show-Fallback: {
    label: "显示后备 UI"
    shape: rectangle
    style.stroke: "#faad14"
  }
}

Network: {
  label: "网络"
  shape: cylinder

  Dynamic-Import: {
    label: "动态 import() promise"
    shape: rectangle
  }
}

Render-Result: {
  label: "最终渲染"
  shape: package

  Render-Component: {
    label: "渲染实际组件"
    shape: rectangle
    style.stroke: "#52c41a"
  }

  Throw-Error: {
    label: "抛出错误"
    shape: rectangle
    style.stroke: "#ff4d4f"
  }
}

Error-Boundary: {
  label: "错误边界"
  shape: rectangle
  tooltip: "捕获渲染错误"
}

App-Render -> Suspense-Boundary.Lazy-Component-Render: "1. 首次尝试渲染"
Suspense-Boundary.Lazy-Component-Render -> Network.Dynamic-Import: "2. 触发 lazyInitializer，状态变为 Pending"
Suspense-Boundary.Lazy-Component-Render -> Suspense-Boundary.Show-Fallback: "3. React 暂停渲染"

Network.Dynamic-Import -> Render-Result.Render-Component: "4a. Promise 解析成功 (状态：Resolved)"
Network.Dynamic-Import -> Render-Result.Throw-Error: "4b. Promise 被拒绝 (状态：Rejected)"

Render-Result.Throw-Error -> Error-Boundary: "5. 错误被捕获"

```

1.  当 React 遇到懒加载组件时，其状态为 `Uninitialized`。React 调用 `_init` 函数。
2.  `_init` 函数执行动态 `import()` 并将组件状态转换为 `Pending`。然后它会抛出 `import()` 调用返回的 promise (`thenable`)。
3.  因为组件尚未就绪，React 会暂停渲染过程。它会沿着组件树向上查找，直到找到最近的 `<Suspense>` 边界，该边界会捕获 promise 并显示其 `fallback` UI。
4.  一旦 promise 成功解析，懒加载组件的状态变为 `Resolved`。React 会收到通知并再次尝试渲染该组件。这一次，`_init` 函数看到 `Resolved` 状态并返回实际组件模块的 `default` 导出，然后将其渲染到屏幕上。
5.  如果 promise 被拒绝，状态变为 `Rejected`。`_init` 函数会抛出该错误。这个错误会沿着树向上传播，并应由一个[错误边界](https://react.dev/reference/react/Component#catching-rendering-errors-with-an-error-boundary)捕获。

## 错误处理

如果懒加载组件加载失败（例如，由于网络错误），它会触发一个错误。你可以通过将懒加载组件包裹在一个错误边界中来优雅地处理这些错误并显示用户友好的信息。错误边界是一个类组件，它可以捕获其子组件树中任何位置的 JavaScript 错误。

```javascript
import React, { Suspense } from 'react';
import ErrorBoundary from './ErrorBoundary'; // 一个自定义的错误边界组件

const OtherComponent = React.lazy(() => import('./OtherComponent'));

function MyApp() {
  return (
    <div>
      <ErrorBoundary>
        <Suspense fallback={<div>Loading...</div>}>
          <OtherComponent />
        </Suspense>
      </ErrorBoundary>
    </div>
  );
}
```

通过结合使用 `lazy`、`Suspense` 和错误边界，你可以创建一个健壮且高性能的用户体验，有效处理加载状态和网络故障。