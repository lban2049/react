# API 参考

本节提供 `react-art` 暴露的所有公共 API 的全面参考。您将找到组件、实用类及其属性的详细描述，有助于深入使用该库。有关如何在实践中使用这些组件的入门指南，请参阅[绘制组件](./drawing-components.md)部分。要了解如何应用样式和处理交互，请查阅[样式和交互性](./styling-interactivity.md)。有关内部类型和事件机制的详细信息，请参阅[内部类型和事件](./core-concepts-internal-types-events.md)。

## 组件

### Surface

`Surface` 组件是所有 ART 元素的根容器。它定义了绘图区域的尺寸，并作为所有其他 ART 组件的渲染画布。在内部，它管理 ART Surface 实例并处理其尺寸的更新。

**Props**

| Name | Type | Description |
|:---|:---|:---|
| `height` | `number` | 绘图表面的高度。设置画布或 SVG 元素的高度必需。 |
| `width` | `number` | 绘图表面的宽度。设置画布或 SVG 元素的宽度必需。 |
| `accessKey` | `string` | 用于可访问性的标准 HTML `accessKey` 属性。 |
| `className` | `string` | 用于根元素样式设置的标准 HTML `className` 属性。 |
| `draggable` | `boolean` | 标准 HTML `draggable` 属性。 |
| `role` | `string` | 用于 ARIA 可访问性的标准 HTML `role` 属性。 |
| `style` | `object` | 用于内联 CSS 的标准 React `style` 对象。 |
| `tabIndex` | `number` | 标准 HTML `tabIndex` 属性。 |
| `title` | `string` | 标准 HTML `title` 属性，通常显示为工具提示。 |

**Usage Example**

```jsx
import { Surface, Shape } from 'react-art';

function MyDrawing() {
  return (
    <Surface width={300} height={200} style={{ border: '1px solid black' }}>
      <Shape d="M0 0 L100 0 L50 100 Z" fill="blue" x={50} y={50} />
    </Surface>
  );
}
```

此示例创建一个带有边框的 300x200 像素绘图表面，并在其中渲染一个蓝色三角形。

### ClippingRectangle

`ClippingRectangle` 组件定义了一个矩形剪裁区域。作为子元素渲染在 `ClippingRectangle` 中的任何 ART 元素将仅在该定义区域内可见。

**Props**

| Name | Type | Description |
|:---|:---|:---|
| `width` | `number` | 剪裁矩形的宽度。 |
| `height` | `number` | 剪裁矩形的高度。 |
| `x` | `number` | 剪裁矩形左上角相对于其父元素的 x 坐标。默认为 `0`。 |
| `y` | `number` | 剪裁矩形左上角相对于其父元素的 y 坐标。默认为 `0`。 |
| `rotation` | `number` | 旋转角度（度）。默认为 `0`。 |
| `originX` | `number` | 旋转和缩放原点的 x 坐标。默认为组件中心。 |
| `originY` | `number` | 旋转和缩放原点的 y 坐标。默认为组件中心。 |
| `scale` | `number` | X 轴和 Y 轴的统一缩放因子。默认为 `1`。 |
| `scaleX` | `number` | X 轴的缩放因子。默认为 `1`。 |
| `scaleY` | `number` | Y 轴的缩放因子。默认为 `1`。 |
| `transform` | `object` | 用于应用复杂变换的 ART `Transform` 对象。会覆盖 `x`、`y`、`rotation`、`scale` 等。 |
| `cursor` | `string` | 鼠标悬停在组件上时应用的 CSS 光标样式。 |
| `title` | `string` | 组件的标题，通常显示为工具提示。 |
| `opacity` | `number` | 组件的不透明度，从 `0`（完全透明）到 `1`（完全不透明）。默认为 `1`。 |
| `visible` | `boolean` | 控制组件的可见性。如果为 `false`，则组件被隐藏。默认为 `true`。 |
| `onClick` | `function` | 点击事件的处理程序。 |
| `onMouseMove` | `function` | 鼠标移动事件的处理程序。 |
| `onMouseOver` | `function` | 鼠标移入事件的处理程序。 |
| `onMouseOut` | `function` | 鼠标移出事件的处理程序。 |
| `onMouseUp` | `function` | 鼠标抬起事件的处理程序。 |
| `onMouseDown` | `function` | 鼠标按下事件的处理程序。 |

**Usage Example**

```jsx
import { Surface, Group, ClippingRectangle, Shape } from 'react-art';

function ClippedContent() {
  return (
    <Surface width={200} height={200}>
      <ClippingRectangle x={50} y={50} width={100} height={100}>
        <Group x={-25} y={-25}> {/* Move group to see clipping effect */}
          <Shape d="M0 0 L150 0 L75 150 Z" fill="red" />
        </Group>
      </ClippingRectangle>
    </Surface>
  );
}
```

此示例绘制一个红色三角形，但只有位于表面 (50, 50) 处 100x100 矩形内的部分可见。

### Group

`Group` 组件用于逻辑上组合多个 ART 元素。将变换、不透明度或事件处理程序等属性应用于 `Group` 将同时影响其所有子元素。组还可以定义自己的 `width` 和 `height`，这可能会影响其子元素的布局和缩放。

**Props**

| Name | Type | Description |
|:---|:---|:---|
| `width` | `number` | 组的宽度。 |
| `height` | `number` | 组的高度。 |
| `x` | `number` | 组左上角相对于其父元素的 x 坐标。默认为 `0`。 |
| `y` | `number` | 组左上角相对于其父元素的 y 坐标。默认为 `0`。 |
| `rotation` | `number` | 旋转角度（度）。默认为 `0`。 |
| `originX` | `number` | 旋转和缩放原点的 x 坐标。默认为组件中心。 |
| `originY` | `number` | 旋转和缩放原点的 y 坐标。默认为组件中心。 |
| `scale` | `number` | X 轴和 Y 轴的统一缩放因子。默认为 `1`。 |
| `scaleX` | `number` | X 轴的缩放因子。默认为 `1`。 |
| `scaleY` | `number` | Y 轴的缩放因子。默认为 `1`。 |
| `transform` | `object` | 用于应用复杂变换的 ART `Transform` 对象。会覆盖 `x`、`y`、`rotation`、`scale` 等。 |
| `cursor` | `string` | 鼠标悬停在组件上时应用的 CSS 光标样式。 |
| `title` | `string` | 组件的标题，通常显示为工具提示。 |
| `opacity` | `number` | 组件的不透明度，从 `0`（完全透明）到 `1`（完全不透明）。默认为 `1`。 |
| `visible` | `boolean` | 控制组件的可见性。如果为 `false`，则组件被隐藏。默认为 `true`。 |
| `onClick` | `function` | 点击事件的处理程序。 |
| `onMouseMove` | `function` | 鼠标移动事件的处理程序。 |
| `onMouseOver` | `function` | 鼠标移入事件的处理程序。 |
| `onMouseOut` | `function` | 鼠标移出事件的处理程序。 |
| `onMouseUp` | `function` | 鼠标抬起事件的处理程序。 |
| `onMouseDown` | `function` | 鼠标按下事件的处理程序。 |

**Usage Example**

```jsx
import { Surface, Group, Shape } from 'react-art';

function GroupExample() {
  return (
    <Surface width={300} height={200}>
      <Group x={50} y={50} rotation={45}>
        <Shape d="M0 0 L50 0 L25 50 Z" fill="purple" />
        <Shape d="M60 0 L110 0 L85 50 Z" fill="orange" />
      </Group>
    </Surface>
  );
}
```

此示例将两个形状分组。整个组被移动到 (50, 50) 并旋转 45 度，这会影响组内的两个形状。

### Shape

`Shape` 组件是最通用的绘图原语，允许您使用类似 SVG 的路径数据绘制自定义路径。它支持各种填充和描边属性。

**Props**

| Name | Type | Description |
|:---|:---|:---|
| `d` | `string` | 类似 SVG 的路径数据字符串（例如，`"M10 10 L20 20 Z"`）。也可以作为 `children` 传入。 |
| `children` | `string` | 提供路径数据的 `d` 替代方案。多个子字符串会连接在一起。 |
| `width` | `number` | 形状的宽度。与 `d` 一起用于缩放。 |
| `height` | `number` | 形状的高度。与 `d` 一起用于缩放。 |
| `fill` | `string` or `object` | 填充颜色（例如，`"#FF0000"`）或填充对象（`LinearGradient`、`RadialGradient`、`Pattern`）。 |
| `stroke` | `string` | 描边颜色。 |
| `strokeWidth` | `number` | 描边的宽度。 |
| `strokeCap` | `string` | 指定在描边时用于开放子路径末端的形状。值：`butt`、`round`、`square`。 |
| `strokeJoin` | `string` | 指定在描边时用于路径拐角的形状。值：`miter`、`round`、`bevel`。 |
| `strokeDash` | `array` | 一个数字数组，指定描边交替的虚线和间隙的长度。 |
| `x` | `number` | 形状左上角相对于其父元素的 x 坐标。默认为 `0`。 |
| `y` | `number` | 形状左上角相对于其父元素的 y 坐标。默认为 `0`。 |
| `rotation` | `number` | 旋转角度（度）。默认为 `0`。 |
| `originX` | `number` | 旋转和缩放原点的 x 坐标。默认为组件中心。 |
| `originY` | `number` | 旋转和缩放原点的 y 坐标。默认为组件中心。 |
| `scale` | `number` | X 轴和 Y 轴的统一缩放因子。默认为 `1`。 |
| `scaleX` | `number` | X 轴的缩放因子。默认为 `1`。 |
| `scaleY` | `number` | Y 轴的缩放因子。默认为 `1`。 |
| `transform` | `object` | 用于应用复杂变换的 ART `Transform` 对象。会覆盖 `x`、`y`、`rotation`、`scale` 等。 |
| `cursor` | `string` | 鼠标悬停在组件上时应用的 CSS 光标样式。 |
| `title` | `string` | 组件的标题，通常显示为工具提示。 |
| `opacity` | `number` | 组件的不透明度，从 `0`（完全透明）到 `1`（完全不透明）。默认为 `1`。 |
| `visible` | `boolean` | 控制组件的可见性。如果为 `false`，则组件被隐藏。默认为 `true`。 |
| `onClick` | `function` | 点击事件的处理程序。 |
| `onMouseMove` | `function` | 鼠标移动事件的处理程序。 |
| `onMouseOver` | `function` | 鼠标移入事件的处理程序。 |
| `onMouseOut` | `function` | 鼠标移出事件的处理程序。 |
| `onMouseUp` | `function` | 鼠标抬起事件的处理程序。 |
| `onMouseDown` | `function` | 鼠标按下事件的处理程序。 |

**Usage Example**

```jsx
import { Surface, Shape } from 'react-art';

function CustomShape() {
  return (
    <Surface width={200} height={200}>
      <Shape
        d="M10 10 C20 20, 40 20, 50 10 S90 0, 100 10"
        fill="#ADFF2F"
        stroke="#32CD32"
        strokeWidth={2}
        x={50}
        y={50}
      />
    </Surface>
  );
}
```

此示例使用三次贝塞尔曲线和平滑三次贝塞尔曲线命令绘制一条曲线路径，填充为酸橙绿色并描边为绿色。

### Text

`Text` 组件在您的 ART 图形中渲染文本。它支持各种字体样式、对齐选项，还可以沿自定义路径渲染文本。

**Props**

| Name | Type | Description |
|:---|:---|:---|
| `children` | `string` | 要显示的文本内容。可以是单个字符串或字符串数组。 |
| `font` | `string` or `object` | 要使用的字体。可以是 CSS 字体字符串（例如，`"12px Arial"`）或对象 `{fontSize, fontStyle, fontVariant, fontWeight, fontFamily}`。 |
| `alignment` | `string` | 文本对齐方式。值：`left`、`center`、`right`。 |
| `path` | `object` | 文本应沿其流动的 ART `Path` 对象。 |
| `fill` | `string` or `object` | 填充颜色（例如，`"#FF0000"`）或填充对象（`LinearGradient`、`RadialGradient`、`Pattern`）。 |
| `stroke` | `string` | 描边颜色。 |
| `strokeWidth` | `number` | 描边的宽度。 |
| `strokeCap` | `string` | 指定在描边时用于开放子路径末端的形状。值：`butt`、`round`、`square`。 |
| `strokeJoin` | `string` | 指定在描边时用于路径拐角的形状。值：`miter`、`round`、`bevel`。 |
| `strokeDash` | `array` | 一个数字数组，指定描边交替的虚线和间隙的长度。 |
| `x` | `number` | 文本左上角相对于其父元素的 x 坐标。默认为 `0`。 |
| `y` | `number` | 文本左上角相对于其父元素的 y 坐标。默认为 `0`。 |
| `rotation` | `number` | 旋转角度（度）。默认为 `0`。 |
| `originX` | `number` | 旋转和缩放原点的 x 坐标。默认为组件中心。 |
| `originY` | `number` | 旋转和缩放原点的 y 坐标。默认为组件中心。 |
| `scale` | `number` | X 轴和 Y 轴的统一缩放因子。默认为 `1`。 |
| `scaleX` | `number` | X 轴的缩放因子。默认为 `1`。 |
| `scaleY` | `number` | Y 轴的缩放因子。默认为 `1`。 |
| `transform` | `object` | 用于应用复杂变换的 ART `Transform` 对象。会覆盖 `x`、`y`、`rotation`、`scale` 等。 |
| `cursor` | `string` | 鼠标悬停在组件上时应用的 CSS 光标样式。 |
| `title` | `string` | 组件的标题，通常显示为工具提示。 |
| `opacity` | `number` | 组件的不透明度，从 `0`（完全透明）到 `1`（完全不透明）。默认为 `1`。 |
| `visible` | `boolean` | 控制组件的可见性。如果为 `false`，则组件被隐藏。默认为 `true`。 |
| `onClick` | `function` | 点击事件的处理程序。 |
| `onMouseMove` | `function` | 鼠标移动事件的处理程序。 |
| `onMouseOver` | `function` | 鼠标移入事件的处理程序。 |
| `onMouseOut` | `function` | 鼠标移出事件的处理程序。 |
| `onMouseUp` | `function` | 鼠标抬起事件的处理程序。 |
| `onMouseDown` | `function` | 鼠标按下事件的处理程序。 |

**Usage Example**

```jsx
import { Surface, Text } from 'react-art';

function GreetingText() {
  return (
    <Surface width={300} height={100}>
      <Text
        x={50}
        y={30}
        font={{ fontSize: 24, fontWeight: 'bold', fontFamily: 'Arial' }}
        fill="#007bff"
        alignment="center"
      >
        Hello, React ART!
      </Text>
    </Surface>
  );
}
```

此示例渲染“Hello, React ART!”，以粗体、24 像素 Arial 字体和蓝色填充，居中显示在 (50, 30) 处。

### 其他绘制组件

React ART 还导出了几个基于核心 `Shape` 原语构建的专用组件，用于常见的几何形式。虽然它们的详细属性在 ART 库本身中定义，但您可以直接通过 `react-art` 导入和使用它们。有关其特定属性的全面文档，请参阅 ART 库的官方文档。

*   **Circle**：渲染圆形。
*   **Rectangle**：渲染带可选边框半径的矩形。
*   **Wedge**：渲染圆弧或部分圆形。

## 实用类

React ART 暴露了底层 ART 库中的几个实用类，主要用于定义复杂的填充和变换。

### LinearGradient

`LinearGradient` 类用于为形状创建线性渐变填充。您可以通过颜色停靠点数组和定义渐变方向的坐标对来实例化它。

**Constructor**

`new LinearGradient(stops, x1, y1, x2, y2)`

| Parameter | Type | Description |
|:---|:---|:---|
| `stops` | `array` | 颜色字符串和偏移数字交替的数组（例如，`['red', 0, 'blue', 1]`）。 |
| `x1`, `y1` | `number` | 渐变向量的起始 x 和 y 坐标。 |
| `x2`, `y2` | `number` | 渐变向量的结束 x 和 y 坐标。 |

**Usage Example**

```jsx
import { Surface, Shape, LinearGradient } from 'react-art';

function GradientShape() {
  const linearGradient = new LinearGradient(
    ['#FF0000', 0, '#0000FF', 1], // Red to Blue
    0, 0, 100, 0 // Horizontal gradient from (0,0) to (100,0)
  );

  return (
    <Surface width={200} height={200}>
      <Shape d="M0 0 L100 0 L100 100 L0 100 Z" fill={linearGradient} x={50} y={50} />
    </Surface>
  );
}
```

此示例创建一个填充有从红色水平过渡到蓝色线性渐变的正方形。

### RadialGradient

`RadialGradient` 类用于创建径向渐变填充。它需要颜色停靠点以及定义渐变内圆和外圆的参数。

**Constructor**

`new RadialGradient(stops, fx, fy, rx, ry, cx, cy)`

| Parameter | Type | Description |
|:---|:---|:---|
| `stops` | `array` | 颜色字符串和偏移数字交替的数组。 |
| `fx`, `fy` | `number` | 焦点（内圆中心）的 x 和 y 坐标。 |
| `rx`, `ry` | `number` | 内圆的 x 和 y 半径。 |
| `cx`, `cy` | `number` | 外圆中心的 x 和 y 坐标。 |

**Usage Example**

```jsx
import { Surface, Shape, RadialGradient } from 'react-art';

function RadialGradientShape() {
  const radialGradient = new RadialGradient(
    ['#FFFF00', 0, '#FFA500', 1], // Yellow to Orange
    50, 50, 0, 0, // Focal point at (50,50), inner radius 0
    50, 50, 50, 50 // Outer circle at (50,50) with radius 50
  );

  return (
    <Surface width={200} height={200}>
      <Shape d="M0 0 L100 0 L100 100 L0 100 Z" fill={radialGradient} x={50} y={50} />
    </Surface>
  );
}
```

此示例创建一个填充有径向渐变的正方形，该渐变从黄色（中心）过渡到橙色（边缘）。

### Pattern

`Pattern` 类允许您使用图像图案填充形状。您需要提供图像 URL 和图案平铺的尺寸。

**Constructor**

`new Pattern(url, width, height, left, top)`

| Parameter | Type | Description |
|:---|:---|:---|
| `url` | `string` | 用作图案的图像 URL。 |
| `width` | `number` | 图案平铺的宽度。 |
| `height` | `number` | 图案平铺的高度。 |
| `left`, `top` | `number` | 图案左上角的 x 和 y 偏移量。 |

**Usage Example**

```jsx
import { Surface, Shape, Pattern } from 'react-art';

function PatternShape() {
  const imagePattern = new Pattern(
    'https://via.placeholder.com/20x20.png?text=Tile', // Placeholder image URL
    20, 20 // Tile dimensions
  );

  return (
    <Surface width={200} height={200}>
      <Shape d="M0 0 L100 0 L100 100 L0 100 Z" fill={imagePattern} x={50} y={50} />
    </Surface>
  );
}
```

此示例创建一个填充有指定 URL 重复图像图案的正方形。

### Path

`Path` 对象（导出为 `Path`）是 ART 库的核心类，用于定义几何路径，通常与 `Shape` 组件的 `d` 属性或 `Text` 组件的 `path` 属性一起使用。它提供了使用一系列命令（例如 `moveTo`、`lineTo`、`arc`、`curveTo`）创建复杂形状的方法。

### Transform

`Transform` 对象（导出为 `Transform`）是 ART 库的一个实用类，用于构建和应用复杂的仿射变换（平移、旋转、缩放、倾斜）。虽然 `Shape` 和 `Group` 等组件接受单独的 `x`、`y`、`rotation` 和 `scale` 属性，但 `transform` 属性允许您提供预构建的 `Transform` 实例以实现更复杂的变换。

## 其他导出

### version

`version` 导出提供了 `react-art` 所基于的 React 库的版本字符串。这对于调试或确保与您的 React 环境兼容性很有用。

**Usage Example**

```jsx
import { version } from 'react-art';

console.log(`React ART is using React version: ${version}`);
```

本节全面概述了 `react-art` 中可用的公共 API，包括详细的组件属性和用于高级图形操作的实用类。有关常见问题和调试的指南，请继续阅读[故障排除和支持](./troubleshooting.md)部分。