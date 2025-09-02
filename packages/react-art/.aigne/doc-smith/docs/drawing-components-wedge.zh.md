# Wedge

React ART 中的 `Wedge` 组件用于绘制各种圆形，包括整圆、部分弧线和扇形。此组件提供对角度和半径的精确控制，使您能够在 `<Surface>` 内创建多样化的图形元素。

要了解 `Wedge` 如何与整体绘图系统集成，您可能会发现查阅根容器 [`Surface`](./drawing-components-surface.md) 和通用路径绘图组件 [`Shape`](./drawing-components-shape.md) 的文档会有帮助。

## How it Works

`Wedge` 的工作原理是为 ART `Shape` 元素计算必要的路径数据（`d` 属性）。它获取您指定的角度和半径，将它们转换为一系列绘图命令，然后使用计算出的路径渲染一个 `Shape`。这种抽象允许您声明所需的圆形几何形状，而无需手动定义复杂的 SVG 路径命令。

## Properties

`Wedge` 组件接受多个属性来定义其外观和几何形状：

| 名称 | 类型 | 描述 | 必需 | 默认 |
|---|---|---|---|---|
| `outerRadius` | `number` | 圆形、弧线或扇形的外半径，以像素为单位。 | Yes | |
| `startAngle` | `number` | 起始角度，以度为单位，相对于12点钟方向（0度指向正上方，顺时针增加）。 | Yes | |
| `endAngle` | `number` | 结束角度，以度为单位，相对于12点钟方向。如果 `endAngle` 等于或大于 `startAngle + 360`，则绘制一个整圆。 | Yes | |
| `innerRadius` | `number` | 内半径，以像素为单位。如果大于 `0`，组件将绘制一个圆环或弧线。如果为 `0` 或省略，则绘制一个实心圆或扇形。 | No | `0` |
| `fill` | `string` | 用于填充扇形的颜色或渐变。接受标准的 CSS 颜色字符串（例如，`'blue'`、`'#FF0000'`、`'rgba(0,0,255,0.5)'`）。 | No | |

## Example Usage

这是一个演示如何绘制蓝色扇形的示例：

```javascript
import React from 'react';
import { Surface, Wedge } from 'react-art';

function MyWedgeComponent() {
  return (
    <Surface width={200} height={200}>
      <Wedge
        outerRadius={80}
        innerRadius={40}
        startAngle={0}
        endAngle={270}
        fill="blue"
      />
    </Surface>
  );
}

export default MyWedgeComponent;
```

此示例渲染一个 200x200 像素的表面，其中包含一个蓝色扇形。该扇形的外半径为 80 像素，内半径为 40 像素，形成一个弧。它从 0 度（12 点钟方向）开始，顺时针延伸到 270 度。如果 `innerRadius` 被省略或设置为 0，它将绘制一个实心圆扇形。

## Drawing a Full Circle

要绘制一个整圆或圆环，请将 `endAngle` 设置为等于或大于 `startAngle + 360`。

```javascript
import React from 'react';
import { Surface, Wedge } from 'react-art';

function MyCircleComponent() {
  return (
    <Surface width={150} height={150}>
      {/* Solid red circle */}
      <Wedge
        outerRadius={70}
        startAngle={0}
        endAngle={360}
        fill="red"
      />
      {/* Green ring */}
      <Wedge
        outerRadius={60}
        innerRadius={50}
        startAngle={0}
        endAngle={720} // Can be any value >= startAngle + 360
        fill="green"
        // Position the ring so it's visible, for example by using a Group
        // or by adjusting Surface size/Wedge position if Surface is larger
      />
    </Surface>
  );
}

export default MyCircleComponent;
```

此示例展示了如何使用 `Wedge` 组件，通过将 `endAngle` 分别设置为 `360` 或 `720` 来渲染一个实心红色圆和绿色圆环。

---

现在您已经了解了如何绘制弧线、圆形和扇形，您可能希望探索其他方式来增强您的图形。继续阅读 [`Styling and Interactivity`](./styling-interactivity.md) 部分，了解如何应用填充、描边和变换，或者深入 [`API Reference`](./api-reference.md) 以获取有关 `react-art` 组件和实用工具的更多详细信息。
