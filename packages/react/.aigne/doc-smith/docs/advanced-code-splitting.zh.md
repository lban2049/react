# 使用 `lazy` 和 `Suspense` 进行代码分割

代码分割是优化 React 应用的一项强大技术。它允许你将代码拆分成更小的代码块，然后在用户实际需要时按需加载。这可以减少初始包体积，从而加快页面加载速度并改善用户体验。React 提供了使用 `React.lazy` 和 `React.Suspense` 实现代码分割的内置方法。

本指南将介绍如何使用这些 API 动态加载组件。

## 使用 `React.lazy` 动态加载组件

`React.lazy` 函数提供了一种将动态导入的组件像常规组件一样进行渲染的简单方法。它同时适用于客户端和服务器环境。

以前，你可能会像下面这样静态导入一个组件：
```javascript
import MyComponent from './MyComponent';
```

使用 `lazy`，你可以将其更改为返回 Promise 的动态导入：

```javascript
import React, { lazy } from 'react';

const MyComponent = lazy(() => import('./MyComponent'));
```

`React.lazy` 接受一个必须调用动态 `import()` 的函数。这个 `import()` 调用会返回一个 Promise，该 Promise 解析为一个包含 React 组件的 `default` 导出模块。如果缺少 `default` 导出，React 将在渲染阶段抛出错误。

### `lazy` 的内部工作原理

`lazy` 组件内部有一个状态机来管理加载过程。该组件的有效负载会经历以下几种状态：

```d2
direction: right

Uninitialized: {
  label: "未初始化"
}
Pending: {
  label: "待定"
  "调用动态 import()"
}
Resolved: {
  label: "已解析"
  "Promise 完成，组件渲染"
}
Rejected: {
  label: "已拒绝"
  "Promise 拒绝，抛出错误"
}

Uninitialized -> Pending: "首次渲染尝试"
Pending -> Resolved: "网络请求成功"
Pending -> Rejected: "网络请求失败"
```

该状态由一个初始化函数管理，该函数负责处理动态导入返回的 Promise，将组件从 `Pending` 状态转换到 `Resolved` 或 `Rejected` 状态。

## 使用 `Suspense` 处理加载状态

`lazy` 组件在加载期间会暂停渲染。`React.Suspense` 允许你指定一个加载指示器（即后备 UI），以便在组件处于 `Pending` 状态时显示。

```javascript
import React, { Suspense, lazy } from 'react';

const OtherComponent = lazy(() => import('./OtherComponent'));

function App() {
  return (
    <div>
      <h1>My Application</h1>
      <Suspense fallback={<div>组件加载中...</div>}>
        <OtherComponent />
      </Suspense>
    </div>
  );
}
```

`fallback` 属性接受任何可渲染的 React 元素。你可以将 `Suspense` 组件放置在 `lazy` 组件上方的任何位置，从而用一个加载指示器包裹多个 `lazy` 组件。

## 基于路由的代码分割

代码分割的一个常见且有效的用例是与路由结合。你可以只在用户导航到应用的特定页面或部分时，才加载相应的代码。

以下是一个概念性示例：

```javascript
import React, { Suspense, lazy } from 'react';
// 假设有一个基础的路由组件
import { Router, Route } from './my-router';

const HomePage = lazy(() => import('./routes/Home'));
const AboutPage = lazy(() => import('./routes/About'));

const App = () => (
  <Router>
    <Suspense fallback={<div>页面加载中...</div>}>
      <Route path="/" component={HomePage} />
      <Route path="/about" component={AboutPage} />
    </Suspense>
  </Router>
);
```

在此设置中，只有当用户访问根路径时，才会获取 `HomePage` 的 JavaScript 代码；同样，只有当用户导航到 `/about` 时，才会获取 `AboutPage` 的代码。

## 使用错误边界处理错误

如果动态导入失败（例如，由于网络错误），它会抛出一个错误。为了优雅地处理这些错误并向用户显示友好的信息，你可以将你的 `lazy` 组件包裹在[错误边界](https://react.dev/reference/react/Component#catching-rendering-errors-with-an-error-boundary)中。

错误边界是一个定义了 `static getDerivedStateFromError()` 或 `componentDidCatch()` 的类组件。

```javascript
import React, { Suspense, lazy } from 'react';
import { MyErrorBoundary } from './MyErrorBoundary';

const BrokenComponent = lazy(() => import('./NonExistentComponent'));

function App() {
  return (
    <div>
      <MyErrorBoundary>
        <Suspense fallback={<div>加载中...</div>}>
          <BrokenComponent />
        </Suspense>
      </MyErrorBoundary>
    </div>
  );
}
```

通过在 `Suspense` 外层包裹一个错误边界，你可以捕获动态导入的失败，并显示一个后备错误 UI，从而防止整个应用崩溃。

---

通过利用 `lazy` 和 `Suspense`，你可以显著提升 React 应用的性能和用户体验。如果想了解更多高级主题，你可能对 [服务器与客户端环境](./advanced-server-vs-client.md) 之间的差异以及 React 如何处理 [缓存](./advanced-caching.md) 感兴趣。