# Rectangle

React ART 中的 `Rectangle` 组件允许你在 `Surface` 内绘制矩形形状。它支持基本的 `width` 和 `height` 属性，以及使用统一的 `radius` 或单独的角半径来创建圆角的功能。

要了解 `Rectangle` 如何适应整体绘图过程，你可以参考 [Shape](./drawing-components-shape.md) 和 [Surface](./drawing-components-surface.md) 文档。

## Basic Usage

你可以通过指定 `width` 和 `height` 来定义一个简单的矩形。与其他 ART 组件一样，你可以应用 `stroke` 用于边框，`fill` 用于内部颜色。

```jsx
import React from 'react';
import { Surface, Rectangle } from 'react-art';

function MyDrawing() {
  return (
    <Surface width={200} height={200}>
      <Rectangle
        width={100}
        height={50}
        stroke="red"
        fill="lightcoral"
      />
    </Surface>
  );
}

export default MyDrawing;
```

此示例创建了一个带有红色描边和浅珊瑚色填充的矩形，其宽度为 100 单位，高度为 50 单位。

## Properties

除了 `fill`、`stroke` 和 `transform` 等常见的 [Shape](./drawing-components-shape.md) 属性之外，`Rectangle` 组件还接受以下特定于其形状的属性。

| Name | Type | Description |
|---|---|---|
| `width` | `number` | 矩形的宽度。负值将使矩形在负 X 方向上偏移。 |
| `height` | `number` | 矩形的高度。负值将使矩形在负 Y 方向上偏移。 |
| `radius` | `number` | 一个可选的统一半径，应用于矩形的所有四个角。如果指定了单独的角半径（例如 `radiusTopLeft`），它们将覆盖此属性以应用于特定角。负值将被视为 0。 |
| `radiusTopLeft` | `number` | 左上角的可选半径。它将覆盖此角的 `radius` 属性。负值将被视为 0。 |
| `radiusTopRight` | `number` | 右上角的可选半径。它将覆盖此角的 `radius` 属性。负值将被视为 0。 |
| `radiusBottomLeft` | `number` | 左下角的可选半径。它将覆盖此角的 `radius` 属性。负值将被视为 0。 |
| `radiusBottomRight` | `number` | 右下角的可选半径。它将覆盖此角的 `radius` 属性。负值将被视为 0。 |

## Example with Rounded Corners

你可以使用 `radius` 或特定的角 `radius` 属性来创建带圆角的矩形。请注意，如果一侧的组合半径超过 `width` 或 `height`，则该侧的半径将默认为 0，以确保形状有效。

```jsx
import React from 'react';
import { Surface, Rectangle } from 'react-art';

function RoundedRectangles() {
  return (
    <Surface width={300} height={200}>
      {/* 具有统一半径的矩形 */}
      <Rectangle
        x={20} y={20} // 定位矩形
        width={80}
        height={80}
        radius={15}
        stroke="blue"
        fill="lightblue"
      />

      {/* 具有不同角半径的矩形 */}
      <Rectangle
        x={150} y={20}
        width={120}
        height={100}
        radiusTopLeft={30}
        radiusBottomRight={50}
        stroke="green"
        fill="lightgreen"
      />
    </Surface>
  );
}

export default RoundedRectangles;
```

此示例展示了两个矩形：一个在所有角上都具有统一的半径，另一个则在其左上角和右下角应用了不同的半径。

---

现在你已经了解如何绘制矩形，你可能想探索 [Wedge](./drawing-components-wedge.md) 组件来绘制弧线和部分形状，或者返回 [Drawing Components](./drawing-components.md) 概述。