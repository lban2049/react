# 使用 `lazy` 和 `Suspense` 进行代码分割

代码分割是一种通过将大型代码包拆分成较小代码块来提升 Web 应用性能的技术。这些代码块可以按需加载，从而减少初始加载时间并改善用户体验。React 提供了 `React.lazy` 和 `Suspense` 这两个核心功能，使代码分割变得简单直接且具有声明性。

本指南将逐步介绍如何使用 `lazy` 和 `Suspense` 来实现组件的按需加载。

## `React.lazy` 函数

`React.lazy` 允许你将动态导入的组件作为常规组件进行渲染。它接受一个函数作为参数，该函数必须调用动态 `import()`。

### 如何使用

`lazy` 函数会返回一个特殊的 `LazyComponent` 对象。该对象具有 React 能够理解的特定结构，其 `$$typeof` 属性值为 `REACT_LAZY_TYPE`。

代码分割前：
```javascript
import MyComponent from './MyComponent';

const App = () => (
  <div>
    <MyComponent />
  </div>
);
```

使用 `React.lazy` 进行代码分割后：
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

`import()` 调用会返回一个 `Promise`（具体来说，是一个 `Thenable` 对象）。此 promise 会解析为一个模块对象，该对象必须包含一个名为 `default` 的导出，且该导出的内容为 React 组件。如果模块没有 `default` 导出，导入操作将会失败。

### 内部状态机

`LazyComponent` 的载荷在内部会经历几个状态来管理异步加载过程。正是这个机制使得 `Suspense` 能够显示后备 UI。

```d2
direction: down

"初始渲染": {
  shape: oval
}

"未初始化": {
  shape: rectangle
  label: "未初始化 (-1)"
}

"待定": {
  shape: rectangle
  label: "待定 (0)"
  "动态 import() promise 正在进行中。"
}

"已解析": {
  shape: rectangle
  label: "已解析 (1)"
  style.fill: "#f6ffed"
  "组件模块已成功加载。"
}

"已拒绝": {
  shape: rectangle
  label: "已拒绝 (2)"
  style.fill: "#fff1f0"
  "组件模块加载失败。"
}

"组件渲染": {
  shape: oval
}

"错误边界": {
  shape: oval
}

"初始渲染" -> "未初始化": "lazy() 创建组件"
"未初始化" -> "待定": "首次渲染触发 lazyInitializer()"
"待定" -> "已解析": "import() promise 解析成功"
"待定" -> "已拒绝": "import() promise 被拒绝"
"已解析" -> "组件渲染": "React 渲染组件的 default 导出"
"已拒绝" -> "错误边界": "React 抛出错误"

```

## `Suspense` 组件

使用 `React.lazy` 创建的组件只能在 `Suspense` 组件内部渲染。`Suspense` 允许你指定一个加载指示器（即 `fallback` 属性），它会在懒加载组件的代码被获取和加载时显示。

```javascript
import React, { Suspense } from 'react';

const OtherComponent = React.lazy(() => import('./OtherComponent'));

function MyComponent() {
  return (
    <div>
      <Suspense fallback={<div>Loading component...</div>}>
        <OtherComponent />
      </Suspense>
    </div>
  );
}
```

`fallback` 属性可以接受任何你希望在等待组件加载时渲染的 React 元素。你可以将 `Suspense` 组件放置在懒加载组件上方的任何位置，从而为多个懒加载组件创建一个共享的加载状态。

## 处理错误

如果动态 `import()` 失败（例如，由于网络错误），将会触发一个错误。为了优雅地处理这些错误并显示用户友好的信息，你可以用一个标准的 React [错误边界](https://react.dev/reference/react/Component#catching-rendering-errors-with-an-error-boundary)来包裹你的懒加载组件（及其 `Suspense` 边界）。

以下是一个 `ErrorBoundary` 组件的示例：

```javascript
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    // 更新 state，以便下一次渲染将显示后备 UI。
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    // 你也可以将错误日志记录到错误报告服务中
    console.error("Uncaught error:", error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      // 你可以渲染任何自定义的后备 UI
      return <h1>Something went wrong.</h1>;
    }

    return this.props.children; 
  }
}
```

然后，你可以用它来包裹你的懒加载组件：

```javascript
import React, { Suspense } from 'react';
import ErrorBoundary from './ErrorBoundary';

const MyLazyComponent = React.lazy(() => import('./MyLazyComponent'));

const App = () => {
  return (
    <div>
      <h1>My Application</h1>
      <ErrorBoundary>
        <Suspense fallback={<div>Loading...</div>}>
          <MyLazyComponent />
        </Suspense>
      </ErrorBoundary>
    </div>
  );
};
```

## 在不同环境中的可用性

`lazy` 函数是 React 的核心部分，它同时为客户端和服务器端环境提供导出。这确保了使用 `lazy` 和 `Suspense` 实现的代码分割能够跨越不同的渲染策略（包括服务器端渲染，即 SSR）无缝工作。

---

通过利用 `React.lazy` 和 `Suspense`，你可以显著提升应用的初始加载性能。这种模式允许你推迟加载非关键组件的代码，直到用户实际需要它们时再加载。

要了解更高级的性能模式，你可以进一步探索[缓存](./advanced-caching.md)和[过渡](./advanced-transitions.md)。