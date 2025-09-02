# 开始使用

本节指导您如何开始使用 `react-cache` 进行演示和实验。作为一个实验性包，`react-cache` 主要用于探索潜在的未来 React 特性，并作为高级缓存实现的参考。要对该包进行高层次的介绍，请参阅[概述](./Overview.md)部分。

**务必理解，`react-cache` 是不稳定的，不适用于实际应用程序。其 API 可能会频繁且显著地发生变化。**

## 安装

`react-cache` 以 Alpha 版本发布，可以添加到您的项目中进行实验。请确保您已安装 `react` 版本 17.0.0 或更高版本，因为它是对等依赖。

您可以使用 npm 或 yarn 进行安装：

```bash
npm install react-cache@2.0.0-alpha.0
# or
yarn add react-cache@2.0.0-alpha.0
```

安装后，您可以将其功能导入到您的 React 应用程序中。

## 基本用法和警告

`react-cache` 暴露了 `unstable_createResource` 用于创建可缓存的数据资源，以及 `unstable_setGlobalCacheLimit` 用于调整缓存大小。使用 `react-cache` 时，请始终记住其实验性质。

以下是创建和使用资源的概念性示例。请注意，`read` 和 `preload` 方法只能在组件的渲染阶段内调用。

```javascript
import { unstable_createResource, unstable_setGlobalCacheLimit } from 'react-cache';

// 定义一个用于获取数据的函数（例如，从 API）
const fetchData = async (id) => {
  // 模拟异步操作
  return new Promise(resolve => {
    setTimeout(() => {
      console.log(`Fetching data for ID: ${id}`);
      resolve(`Data for ${id}`);
    }, 1000);
  });
};

// 使用 unstable_createResource 创建可缓存资源
// 第一个参数是获取器函数。
// 第二个可选参数是用于复杂键的哈希函数。
const myResource = unstable_createResource(fetchData);

// 您可以调整全局缓存限制。默认值为 500。
unstable_setGlobalCacheLimit(1000);

function MyComponent({ itemId }) {
  // 在实际场景中，您会在渲染方法内调用 read()
  // 为了演示，这是您访问缓存数据或触发获取的方式。
  try {
    const data = myResource.read(itemId);
    return <div>Displaying: {data}</div>;
  } catch (suspender) {
    // 如果数据尚未解析，read() 将抛出 suspender
    // React 的 Suspense 组件将捕获此错误。
    return <div>Loading data for {itemId}...</div>;
  }
}

// 预加载数据（例如，在悬停时或组件生命周期的早期）
// myResource.preload('anotherId');

// 重要提示：请勿在生产环境中使用 react-cache。这仅用于演示和学习目的。
```

此示例演示了定义可缓存数据源的主要入口点 `unstable_createResource`。`read` 方法旨在在 React 的渲染阶段内调用，从而实现 Suspense 驱动的数据加载。

## 后续步骤

设置好 `react-cache` 以进行实验后，您现在可以更详细地探索其内部机制和 API。请前往[核心概念](./Core-Concepts.md)部分以了解其基本原理，或者直接查阅[API 参考](./API-Reference.md)以获取 `unstable_createResource` 和 `unstable_setGlobalCacheLimit` 的详细文档。