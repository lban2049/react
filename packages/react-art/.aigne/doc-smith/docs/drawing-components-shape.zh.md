# Shape

React ART 中的 `Shape` 组件提供了一种通用方式，通过路径定义来绘制自定义矢量图形。它允许您通过定义一系列绘图命令来创建超越基本圆形或矩形的复杂几何形状。此组件对于渲染自定义插图和视觉元素至关重要。

有关所有绘图组件的概述，请参阅[绘图组件](./drawing-components.md)部分。要了解如何应用颜色和轮廓，请参阅[填充和描边](./styling-interactivity-fills-strokes.md)。

## Custom Path Data

`Shape` 组件的核心在于它能够解释路径数据，这些数据决定了它所呈现的形状。您可以通过 `d` 属性或作为 `Shape` 组件的子元素来提供此路径数据。

*   **`d` 属性**：此属性接受一个表示路径命令的字符串。这些命令类似于 SVG 路径数据，定义了移动（例如，`M` 表示 moveTo）、线条（例如，`L` 表示 lineTo）、曲线（例如，`C` 表示 cubic Bézier）和弧线。
*   **子元素**：或者，您可以直接在 `Shape` 组件内部提供字符串子元素作为路径数据。如果提供了多个子元素，它们将连接在一起形成完整的路径字符串。

`Shape` 组件使用此路径数据来绘制图形的轮廓，然后可以进行填充和描边。

## Properties

除了路径定义之外，`Shape` 组件还接受各种属性来控制其外观、位置和交互性。其中许多属性继承自通用的 ART 节点属性，影响形状的渲染和转换方式。

| Name | Type | Description |
|---|---|---|
| `d` | `string` | 定义形状几何图形的路径数据字符串。例如，`"M10 10 L100 10 L100 100 L10 100 Z"` 表示一个正方形。 |
| `children` | `string` or `Array<string>` | 提供路径数据的另一种方式。字符串子元素将被连接起来以形成路径。 |
| `width` | `number` | 形状边界框的宽度。 |
| `height` | `number` | 形状边界框的高度。 |
| `fill` | `string` or `object` | 定义形状的填充颜色或渐变/图案对象。可以是颜色字符串（例如 `"red"`）或 `LinearGradient`、`RadialGradient` 或 `Pattern` 的实例。 |
| `stroke` | `string` | 定义形状描边（轮廓）的颜色。 |
| `strokeWidth` | `number` | 描边的宽度。 |
| `strokeCap` | `string` | 开放子路径末端笔帽的样式（例如，`"butt"`、`"round"`、`"square"`）。 |
| `strokeJoin` | `string` | 拐角样式（例如，`"miter"`、`"round"`、`"bevel"`）。 |
| `strokeDash` | `Array<number>` | 一个数字数组，指定描边虚线和间隙的交替长度。 |
| `x` | `number` | 形状平移的 x 坐标。 |
| `y` | `number` | 形状平移的 y 坐标。 |
| `rotation` | `number` | 旋转角度（以度为单位）。 |
| `originX` | `number` | 旋转和缩放中心的 x 坐标。 |
| `originY` | `number` | 旋转和缩放中心的 y 坐标。 |
| `scale` | `number` | 统一缩放因子。 |
| `scaleX` | `number` | 沿 x 轴的缩放因子。 |
| `scaleY` | `number` | 沿 y 轴的缩放因子。 |
| `transform` | `object` | 用于自定义转换的 ART `Transform` 对象。 |
| `opacity` | `number` | 形状的不透明度（0 到 1）。 |
| `visible` | `boolean` | 控制形状的可见性。`true` 表示可见（默认），`false` 表示隐藏。 |
| `cursor` | `string` | 鼠标悬停在形状上时的光标样式（例如，`"pointer"`）。 |
| `title` | `string` | 形状的工具提示标题。 |
| `onClick` | `function` | 点击事件的事件处理程序。 |
| `onMouseMove` | `function` | 鼠标移动事件的事件处理程序。 |
| `onMouseOver` | `function` | 鼠标悬停事件的事件处理程序。 |
| `onMouseOut` | `function` | 鼠标移出事件的事件处理程序。 |
| `onMouseUp` | `function` | 鼠标抬起事件的事件处理程序。 |
| `onMouseDown` | `function` | 鼠标按下事件的事件处理程序。 |

## Usage Example

此示例演示了如何使用 `d` 属性绘制一个简单的三角形 `Shape` 组件，并应用实心填充颜色和描边。

```javascript
import React from 'react';
import { Surface, Shape } from 'react-art';

function TriangleArt() {
  return (
    <Surface width={200} height={200}>
      {/* Defines a path for a triangle */}
      <Shape
        d="M100 10 L190 190 L10 190 Z"
        fill="blue"
        stroke="darkblue"
        strokeWidth={5}
      />
    </Surface>
  );
}

export default TriangleArt;
```

此代码在 200x200 像素的表面上渲染一个带有深蓝色轮廓的蓝色三角形。`d` 属性指定了路径：`M100 10` 移动到 (100, 10)，`L190 190` 绘制一条线到 (190, 190)，`L10 190` 绘制一条线到 (10, 190)，`Z` 关闭路径回到起点。

## Conclusion

`Shape` 组件对于在 React ART 中创建自定义、复杂的矢量图形至关重要。通过掌握其路径数据和样式属性，您可以绘制应用程序视觉效果所需的几乎任何形状。

要了解有关对元素进行分组以实现更简单的转换和管理的更多信息，请继续阅读[Group](./drawing-components-group.md)部分。