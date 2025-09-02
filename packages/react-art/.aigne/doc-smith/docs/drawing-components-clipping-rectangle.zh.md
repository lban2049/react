# 裁剪矩形

React ART 中的 `ClippingRectangle` 组件定义了一个矩形区域，用于裁剪其子元素。这意味着子元素中超出 `ClippingRectangle` 边界的任何部分都不会被渲染。此功能对于创建复杂布局、遮罩内容或管理 ART 图形中的溢出非常有用。它的操作类似于视口或遮罩，将其子元素限制在其定义的边界内。

要了解 `ClippingRectangle` 如何融入绘图元素的更广泛结构，请参阅[绘图组件](./drawing-components.md)概述。对于不带裁剪的一般分组和变换，请参阅 [Group](./drawing-components-group.md) 组件。

## 创建裁剪矩形

你可以通过使用从 `react-art` 导出的 `ClippingRectangle` 组件来创建 `ClippingRectangle`。在内部，此组件会实例化一个 ART `ClippingRectangle` 对象，然后由 React ART 的协调器进行管理。

## 属性

`ClippingRectangle` 组件接受特定属性来定义其裁剪区域，并且它还继承了 ART 基础节点类型的通用属性集，从而允许进行定位、变换和交互。

### 裁剪特有属性

这些属性直接控制裁剪区域的尺寸：

| Name | Type | Description |
|---|---|---|
| `width` | `number` | 裁剪矩形的宽度。 |
| `height` | `number` | 裁剪矩形的高度。 |

### 继承的节点属性

`ClippingRectangle` 组件还支持以下属性，这些属性是许多 ART 绘图组件共有的，可用于控制位置、外观和交互性。

| Name | Type | Description |
|---|---|---|
| `x` | `number` | 裁剪矩形位置的 x 坐标。默认值为 `0`。 |
| `y` | `number` | 裁剪矩形位置的 y 坐标。默认值为 `0`。 |
| `rotation` | `number` | 围绕 `originX` 和 `originY` 的旋转角度，单位为度。默认值为 `0`。 |
| `originX` | `number` | 旋转原点的 x 坐标。如果未设置，则围绕组件的 `x` 位置进行旋转。 |
| `originY` | `number` | 旋转原点的 y 坐标。如果未设置，则围绕组件的 `y` 位置进行旋转。 |
| `scale` | `number` | 围绕 `originX` 和 `originY` 对 x 轴和 y 轴施加的统一缩放因子。默认值为 `1`。 |
| `scaleX` | `number` | 应用于 x 轴的缩放因子。如果也设置了 `scale`，则 `scaleX` 优先。默认值为 `1`。 |
| `scaleY` | `number` | 应用于 y 轴的缩放因子。如果也设置了 `scale`，则 `scaleY` 优先。默认值为 `1`。 |
| `transform` | `Transform` | 用于自定义变换矩阵的 ART `Transform` 对象。它在 `x`、`y`、`rotation` 和 `scale` 之后应用。 |
| `opacity` | `number` | 一个介于 `0` 和 `1` 之间的值，表示组件的不透明度。`0` 表示完全透明，`1` 表示完全不透明。默认值为 `1`。 |
| `visible` | `boolean` | 控制组件的可见性。`true`（默认）表示可见，`false` 表示隐藏。 |
| `cursor` | `string` | 设置当鼠标悬停在组件上时的鼠标光标样式（例如，`'pointer'`、`'grab'`）。 |
| `title` | `string` | 鼠标悬停时显示的标题或工具提示文本。 |
| `onClick` | `function` | 鼠标点击事件的事件处理程序。 |
| `onMouseMove` | `function` | 鼠标移动事件的事件处理程序。 |
| `onMouseOver` | `function` | 鼠标移入事件的事件处理程序。 |
| `onMouseOut` | `function` | 鼠标移出事件的事件处理程序。 |
| `onMouseUp` | `function` | 鼠标抬起事件的事件处理程序。 |
| `onMouseDown` | `function` | 鼠标按下事件的事件处理程序。 |

## 示例

这是一个演示如何使用 `ClippingRectangle` 裁剪 `Shape` 组件的示例。在此场景中，只有红色矩形中落在 `ClippingRectangle` 的 `100x100` 区域内的部分才会可见。

```javascript
import React from 'react';
import { Surface, ClippingRectangle, Shape, Path } from 'react-art';

const ClippingExample = () => {
  const largeRectanglePath = Path()
    .rect(0, 0, 200, 200); // A 200x200 rectangle

  return (
    <Surface width={300} height={300}>
      {/* Clipping rectangle: only 100x100 area at (50,50) is visible */}
      <ClippingRectangle x={50} y={50} width={100} height={100}>
        {/* A large red shape, positioned at (0,0) relative to ClippingRectangle's origin */}
        <Shape 
          d={largeRectanglePath}
          fill="red"
          stroke="black"
          strokeWidth={2}
        />
      </ClippingRectangle>

      {/* Optional: Add a visual indicator of the clipping rectangle's bounds */}
      <Shape 
        d={Path().rect(50, 50, 100, 100)}
        stroke="blue"
        strokeWidth={1}
        opacity={0.5}
      />
    </Surface>
  );
};

export default ClippingExample;
```

在此示例中，`ClippingRectangle` 定位在 `(50, 50)`，尺寸为 `width={100}` 和 `height={100}`。子 `Shape` 绘制一个 200x200 的红色矩形，其起始点相对于其父 `ClippingRectangle` 的原点为 `(0,0)`。因此，只有红色矩形的左上角 100x100 部分会可见，从而有效地裁剪了其余部分。蓝色轮廓线提供了裁剪边界的视觉参考。

---

借助 `ClippingRectangle`，你可以精确控制 ART 元素的可见区域，从而实现更简洁的布局和专业的视觉效果。要了解如何在 ART 图形中渲染文本，请继续阅读 [Text](./drawing-components-text.md) 部分。