# 快速入门

本节将指导您设置 React ART，从安装到渲染您的第一个图形。您将学习如何在 React 应用程序中快速开始绘制基本矢量图形。

## 安装

要使用 React ART，您需要安装 Node.js 和包管理器（如 npm 或 yarn）。React ART 旨在与 React 配合使用，因此请确保您也安装了兼容的 React 版本。

使用 npm 或 yarn 安装 `react-art` 及其对等依赖 `react`：

```bash
npm install react-art react
# or
yarn add react-art react
```

要验证安装，您可以在 Node.js 环境或应用程序代码中从 `react-art` 导入 `version` 并打印其值：

```javascript
import { version } from 'react-art';

console.log(`React ART Version: ${version}`);
// 预期输出：React ART Version: 19.1.0（或类似版本，取决于安装版本）
```

## 您的第一个 React ART 图形

React ART 使用 `Surface` 组件作为所有矢量图形的根容器。在 `Surface` 内部，您可以放置各种绘图组件，例如 `Circle`、`Rectangle`、`Shape`、`Group` 和 `Text`。

让我们创建一个简单的 React 组件，它渲染一个带红色描边的蓝色圆形。首先，请确保您有一个包含根元素（例如 `<div id="root"></div>`）的 HTML 文件。

然后，创建您的 React 组件：

```jsx
import React from 'react';
import { createRoot } from 'react-dom/client';
import { Surface, Circle } from 'react-art';

const container = document.getElementById('root');
const root = createRoot(container);

function MyFirstArtApp() {
  return (
    <Surface width={200} height={200}>
      {/* 渲染一个带有红色描边的蓝色圆形 */}
      <Circle
        x={100} // 圆心相对于 Surface 的 X 坐标
        y={100} // 圆心相对于 Surface 的 Y 坐标
        radius={50} // 圆的半径
        fill="blue" // 圆的填充颜色
        stroke="red" // 圆的描边颜色
        strokeWidth={2} // 描边宽度
      />
    </Surface>
  );
}

root.render(<MyFirstArtApp />);
```

这个示例渲染了一个宽度和高度为 200 像素的 `Surface`。在其中，绘制了一个 `Circle` 组件。`x` 和 `y` 属性指定圆的中心，`radius` 定义其大小，而 `fill`、`stroke` 和 `strokeWidth` 则控制其外观。`Surface` 组件会自动为您的环境选择最合适的渲染后端（Canvas、SVG 或 VML），从而抽象化底层的绘图技术。

## 总结

您已成功设置 React ART 并渲染了您的第一个基本图形。这是创建更复杂和交互式矢量图形的基础。

## 后续步骤

要更深入地了解 React ART 的工作原理及其底层原理，请前往 [核心概念](./core-concepts.md) 部分。
