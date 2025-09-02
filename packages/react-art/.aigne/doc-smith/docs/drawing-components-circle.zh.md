# Circle

The `Circle` component in React ART 中提供了一种直接的方式来绘制圆形。像其他绘图组件一样，它必须在 [`Surface`](./drawing-components-surface.md) 组件内渲染。`Circle` 组件通过抽象底层的 `Path` 创建，简化了创建圆形路径的过程，让你能够专注于圆形的视觉属性。

为了更深入地了解形状如何构建和样式化，请参考 [`Shape`](./drawing-components-shape.md) 文档。

## Usage

要使用 `Circle` 组件，你通常需要指定其 `radius`，并可以选择性地提供样式属性，例如 `stroke`、`strokeWidth` 和 `fill`。这些样式属性会传递给 `Circle` 内部渲染的 `Shape` 组件。

```jsx
import { Surface, Circle } from 'react-art';

function MyCircleGraphic() {
  return (
    <Surface width={200} height={200}>
      <Circle
        x={100}
        y={100}
        radius={50}
        stroke="#FF6347" // 番茄红轮廓
        strokeWidth={5}
        fill="#6A5ACD"   // 石板蓝填充
      />
    </Surface>
  );
}

export default MyCircleGraphic;
```

此示例渲染了一个以 `(100, 100)` 为中心、半径为 50 个单位的圆形，具有红色轮廓、指定的轮廓宽度和蓝色填充。

## Properties

除了通用 [`Shape`](./drawing-components-shape.md) 组件支持的所有属性外，`Circle` 组件还接受以下特定属性。

| Name | Type | Description |
|---|---|---|
| `radius` | `number` | 圆形的半径，以逻辑单位表示。这是一个必需属性。 |

你可以传递给 `Circle` 的常用 `Shape` 属性包括：

| Name | Type | Description |
|---|---|---|
| `x` | `number` | 圆心相对于其父 `Group` 或 `Surface` 的 x 坐标。 |
| `y` | `number` | 圆心相对于其父 `Group` 或 `Surface` 的 y 坐标。 |
| `fill` | `string` or `ARTGradient` or `ARTPattern` | 用于填充圆形的颜色或图案。可以是颜色字符串（例如 "red", "#RRGGBB"）、渐变或图案。 |
| `stroke` | `string` or `ARTGradient` | 圆形轮廓的颜色或渐变。 |
| `strokeWidth` | `number` | 圆形轮廓的宽度。 |
| `opacity` | `number` | 圆形的透明度（0.0 到 1.0）。 |
| `transform` | `ARTTransform` | 一个 `ARTTransform` 对象，用于应用平移、旋转和缩放等变换。 |

## Example

以下是一个示例，演示了具有各种属性的 `Circle` 组件以及如何在 `Group` 中应用变换。

```jsx
import React from 'react';
import { Surface, Group, Circle, Transform } from 'react-art';

function AnimatedCircle() {
  const [rotation, setRotation] = React.useState(0);

  React.useEffect(() => {
    const interval = setInterval(() => {
      setRotation(prevRotation => (prevRotation + 1) % 360);
    }, 20);
    return () => clearInterval(interval);
  }, []);

  const circleTransform = new Transform().rotate(rotation, 50, 50);

  return (
    <Surface width={300} height={300}>
      <Group x={100} y={100}> {/* 偏移组以使圆形居中 */}
        <Circle
          radius={40}
          stroke="darkgreen"
          strokeWidth={2}
          fill="lightgreen"
          transform={circleTransform}
        />
      </Group>
      <Group x={200} y={100}> {/* 另一个圆形用于比较 */}
        <Circle
          radius={30}
          stroke="purple"
          strokeWidth={3}
          fill="pink"
        />
      </Group>
    </Surface>
  );
}

export default AnimatedCircle;
```

此示例创建了一个持续围绕其中心点旋转的动画圆形。它还包含一个用于比较的静态圆形。`Transform` 对象用于应用旋转，演示了如何动态控制视觉效果。

---

本节详细介绍了 `Circle` 组件，包括其用于绘制圆形的使用方法和属性。现在你可以在 React ART 图形中创建基本的圆形元素了。接下来，你可以探索 [`Rectangle`](./drawing-components-rectangle.md) 组件来绘制矩形，或者深入了解 [Styling and Interactivity](./styling-interactivity.md) 以增强你的图形。
