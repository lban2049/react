# 填充与描边

在 React ART 中，你可以使用 `fill` 和 `stroke` 属性为你的绘图组件应用各种视觉样式。这些属性分别控制形状的内部颜色或图案以及其边框的外观。了解如何利用纯色、渐变和图案，以及各种描边自定义设置，可以创建丰富而动态的图形。

有关核心绘图组件的概述，请参阅[绘图组件](./drawing-components.md)部分。要了解元素的定位和大小调整，请参阅[变换](./styling-interactivity-transformations.md)。

## 应用纯色填充和描边

为 ART 元素设置样式的最基本方法是为其 `fill` 和 `stroke` 属性应用纯色。你可以使用标准 CSS 颜色名称（例如，`"red"`）或十六进制颜色代码（例如，`"#FF0000"`）指定颜色。

### `fill` 属性

`fill` 属性确定用于绘制形状内部的颜色或图案。

**参数**

| Name | Type | Description |
|---|---|---|
| `fill` | `string` 或 `object` | 指定填充颜色（字符串形式）或渐变/图案对象（例如，`LinearGradient`、`RadialGradient`、`Pattern`）。 |

**示例：纯色填充**

```javascript
import * as React from 'react';
import { Surface, Shape } from 'react-art';

function SolidFillExample() {
  return (
    <Surface width={200} height={200}>
      <Shape
        d="M10 10 L190 10 L100 190 Z" // A simple triangle path
        fill="#4CAF50" // Green fill
      />
    </Surface>
  );
}

export default SolidFillExample;
```

此示例使用十六进制颜色代码填充，渲染了一个绿色三角形。

### `stroke` 属性

`stroke` 属性定义形状边框（轮廓）的颜色。

**参数**

| Name | Type | Description |
|---|---|---|
| `stroke` | `string` | 将描边颜色指定为字符串。 |

**示例：纯色描边**

```javascript
import * as React from 'react';
import { Surface, Shape } from 'react-art';

function SolidStrokeExample() {
  return (
    <Surface width={200} height={200}>
      <Shape
        d="M10 10 L190 10 L100 190 Z"
        fill="none" // No fill
        stroke="#2196F3" // Blue stroke
        strokeWidth={3}
      />
    </Surface>
  );
}

export default SolidStrokeExample;
```

此示例绘制了一个带有蓝色描边且无填充的三角形轮廓。

## 填充的渐变

React ART 支持线性渐变和径向渐变，允许你在形状内创建平滑的颜色过渡。这些渐变作为 `LinearGradient` 和 `RadialGradient` 对象实例化，并传递给 `fill` 属性。

### `LinearGradient`

`LinearGradient` 沿直线过渡颜色。你可以定义其颜色、它们的位置（色标）以及渐变的起始/结束点。

**构造函数**

`new LinearGradient(stops, x1, y1, x2, y2)`

**参数**

| Name | Type | Description |
|---|---|---|
| `stops` | `Array<string | Array<string | number>>` | 颜色停止点的数组。每个停止点可以是颜色字符串（例如，`"red"`）或数组 `[color, offset]`，其中 `offset`（0-1）指定颜色在渐变中的位置。 |
| `x1`, `y1` | `number` | 渐变线的起始 x 和 y 坐标。 |
| `x2`, `y2` | `number` | 渐变线的结束 x 和 y 坐标。 |

**示例：线性渐变填充**

```javascript
import * as React from 'react';
import { Surface, Shape, LinearGradient } from 'react-art';

function LinearGradientExample() {
  const gradient = new LinearGradient(
    [['#FF0000', 0], ['#FFFF00', 0.5], ['#0000FF', 1]], // Red to Yellow to Blue
    0, 0, // Start X, Y
    200, 200 // End X, Y
  );

  return (
    <Surface width={200} height={200}>
      <Shape
        d="M0 0 H200 V200 H0 Z" // A rectangle path
        fill={gradient}
      />
    </Surface>
  );
}

export default LinearGradientExample;
```

此示例使用从红到黄再到蓝的线性渐变填充一个矩形。

### `RadialGradient`

`RadialGradient` 以圆形或椭圆形过渡颜色，从中心点向外辐射。

**构造函数**

`new RadialGradient(stops, fx, fy, rx, ry, cx, cy)`

**参数**

| Name | Type | Description |
|---|---|---|
| `stops` | `Array<string | Array<string | number>>` | 颜色停止点数组，与 `LinearGradient` 相同。 |
| `fx`, `fy` | `number` | 渐变的焦点（内圆中心）。 |
| `rx`, `ry` | `number` | 渐变结束形状的 x 和 y 半径。 |
| `cx`, `cy` | `number` | 外圆/椭圆的中心点。 |

**示例：径向渐变填充**

```javascript
import * as React from 'react';
import { Surface, Shape, RadialGradient } from 'react-art';

function RadialGradientExample() {
  const gradient = new RadialGradient(
    [['#FFFFFF', 0], ['#00FF00', 0.5], ['#000000', 1]], // White to Green to Black
    100, 100, // Focal X, Y
    100, 100, // Radius X, Y
    100, 100 // Center X, Y
  );

  return (
    <Surface width={200} height={200}>
      <Shape
        d="M0 0 H200 V200 H0 Z" // A rectangle path
        fill={gradient}
      />
    </Surface>
  );
}

export default RadialGradientExample;
```

此示例创建了一个径向渐变填充，从矩形中心向外辐射。

## 填充图案

`Pattern` 允许你使用重复图像填充形状。这对于纹理或重复背景元素很有用。

**构造函数**

`new Pattern(url, width, height, left, top)`

**参数**

| Name | Type | Description |
|---|---|---|
| `url` | `string` | 用作图案的图像 URL。 |
| `width` | `number` | 图案瓦片的宽度。 |
| `height` | `number` | 图案瓦片的高度。 |
| `left` | `number` | 图案的水平偏移量。 |
| `top` | `number` | 图案的垂直偏移量。 |

**示例：图案填充**

```javascript
import * as React from 'react';
import { Surface, Shape, Pattern } from 'react-art';

// Note: Replace with a valid image URL that is publicly accessible.
const imageUrl = 'https://react.dev/favicon.ico'; // Example image

function PatternFillExample() {
  const pattern = new Pattern(imageUrl, 32, 32, 0, 0);

  return (
    <Surface width={200} height={200}>
      <Shape
        d="M0 0 H200 V200 H0 Z" // A rectangle path
        fill={pattern}
      />
    </Surface>
  );
}

export default PatternFillExample;
```

此示例使用指定图像填充一个带有重复图案的矩形。

## 描边自定义

除了颜色之外，你还可以控制形状描边的各个方面，包括其宽度、末端样式（端点）以及拐角渲染方式（连接）。

### 描边属性

| Name | Type | Description |
|---|---|---|
| `strokeWidth` | `number` | 描边的粗细。默认值为 1。 |
| `strokeCap` | `string` | 定义开放路径末端的形状。可能的值：`"butt"`（平直端点，延伸到路径的精确末端），`"round"`（圆形端点），`"square"`（平直端点，超出路径一半描边宽度）。 |
| `strokeJoin` | `string` | 定义连接两条线段的形状。可能的值：`"miter"`（尖角），`"round"`（圆角），`"bevel"`（斜角）。 |
| `strokeDash` | `Array<number>` | 一个数字数组，指定交替的虚线和间隙的长度。例如，`[5, 5]` 创建一个由 5 单位虚线和 5 单位间隙组成的虚线。 |

**示例：自定义描边**

```javascript
import * as React from 'react';
import { Surface, Shape } from 'react-art';

function StrokeCustomizationExample() {
  return (
    <Surface width={300} height={300}>
      {/* Thick stroke with round caps and joins */}
      <Shape
        d="M20 20 L100 20 L100 80"
        stroke="#E91E63"
        strokeWidth={10}
        strokeCap="round"
        strokeJoin="round"
      />

      {/* Dashed stroke */}
      <Shape
        d="M20 120 L180 120"
        stroke="#9C27B0"
        strokeWidth={2}
        strokeDash={[5, 5]} // 5 units dash, 5 units gap
      />

      {/* Different stroke joins */}
      <Shape
        d="M20 200 L100 200 L100 260"
        stroke="#FFC107"
        strokeWidth={8}
        strokeJoin="miter"
      />
      <Shape
        d="M120 200 L200 200 L200 260"
        stroke="#FFC107"
        strokeWidth={8}
        strokeJoin="round"
      />
      <Shape
        d="M220 200 L300 200 L300 260"
        stroke="#FFC107"
        strokeWidth={8}
        strokeJoin="bevel"
      />
    </Surface>
  );
}

export default StrokeCustomizationExample;
```

此示例演示了各种描边属性：带有圆形端点和圆角的粗描边、虚线，以及应用于相似路径进行比较的不同 `strokeJoin` 样式（斜接、圆角、斜面）。

---

本节介绍了如何使用纯色、线性渐变、径向渐变和图案来填充形状，以及如何使用宽度、端点、连接和虚线等属性自定义描边。这些样式选项为 React ART 图形的视觉呈现提供了精细的控制。接下来，请在[变换](./styling-interactivity-transformations.md)部分探索如何对元素应用动态定位和大小调整。