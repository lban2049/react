# 文本

`Text` 组件在 React ART 中用于在图形内渲染文本内容。它提供了对文本字符串本身、字体样式、对齐方式以及沿路径放置文本的控制。理解 `Text` 组件对于创建信息丰富且视觉吸引力的 ART 应用程序至关重要。

有关绘制组件如何适应 React ART 生态系统的通用信息，请参阅[绘制组件概述](./drawing-components.md)。要了解适用于 `Text` 和其他组件的样式功能，请参阅[样式和交互性](./styling-interactivity.md)。

## 概述

`Text` 组件封装了一个 ART `Text` 节点，它是一个负责显示文本的可渲染元素。它利用 ART 的功能，根据当前渲染模式，将文本直接绘制到 Canvas、SVG 或 VML 表面上。

### 访问 `Text` 属性

您可以通过 `ref` 直接从 `Text` 组件实例访问渲染文本的某些尺寸属性。这允许您在 ART 渲染文本后检索其实际的 `height`、`width`、`x` 和 `y` 坐标。

```javascript
import * as React from 'react';
import { Surface, Group, Text } from 'react-art';

class MyTextComponent extends React.Component {
  textRef = React.createRef();

  componentDidMount() {
    if (this.textRef.current) {
      console.log('Text width:', this.textRef.current.width);
      console.log('Text height:', this.textRef.current.height);
    }
  }

  render() {
    return (
      <Surface width={300} height={100}>
        <Group x={50} y={50}>
          <Text ref={this.textRef} font="18px Arial" fill="#000000">
            Hello, React ART!
          </Text>
        </Group>
      </Surface>
    );
  }
}

export default MyTextComponent;
```

此示例演示了如何使用 `ref` 在 `Text` 元素挂载后访问其渲染的 `width` 和 `height` 属性。

## 属性

`Text` 组件接受多个属性来控制其外观和行为。其中许多属性与其它可渲染的 ART 节点通用。

| Name | Type | Description | Default | Applicable to |
|---|---|---|---|---|
| `children` | `string` or `number` | 要显示的实际文本内容。可以是字符串或数字。 | `''` (空字符串) | Text |
| `font` | `string` or `object` | 定义字体样式。可以是类似 CSS 的字符串（例如，`'12px Arial'`）或包含 `fontSize`、`fontStyle`、`fontVariant`、`fontWeight`、`fontFamily` 等属性的对象。 | `undefined` | Text |
| `alignment` | `string` | 指定文本对齐方式。常用值包括 `'left'`、`'center'`、`'right'`。 | `undefined` | Text |
| `path` | `Path` or `string` | 一个 `Path` 对象或字符串，表示文本应沿其渲染的路径。 | `undefined` | Text |
| `x` | `number` | 文本相对于其父组或曲面的 X 坐标。 | `0` | 节点 (继承) |
| `y` | `number` | 文本相对于其父组或曲面的 Y 坐标。 | `0` | 节点 (继承) |
| `rotation` | `number` | 围绕文本原点的旋转角度（度）。 | `0` | 节点 (继承) |
| `originX` | `number` | 旋转和缩放原点的 X 坐标。 | `undefined` | 节点 (继承) |
| `originY` | `number` | 旋转和缩放原点的 Y 坐标。 | `undefined` | 节点 (继承) |
| `scale` | `number` | 均匀缩放因子。 | `1` | 节点 (继承) |
| `scaleX` | `number` | X 轴缩放因子。覆盖 X 轴的 `scale`。 | `1` | 节点 (继承) |
| `scaleY` | `number` | Y 轴缩放因子。覆盖 Y 轴的 `scale`。 | `1` | 节点 (继承) |
| `transform` | `Transform` | 用于自定义矩阵变换的 `ART.Transform` 对象。在 `x`、`y`、`rotation`、`scale` 之后应用。 | `undefined` | 节点 (继承) |
| `fill` | `string` or `LinearGradient` or `RadialGradient` or `Pattern` | 文本的填充颜色或模式。可以是 CSS 颜色字符串，或 `LinearGradient`、`RadialGradient` 或 `Pattern` 的实例。 | `undefined` | 可渲染节点 (继承) |
| `stroke` | `string` | 文本轮廓的描边颜色。 | `undefined` | 可渲染节点 (继承) |
| `strokeWidth` | `number` | 描边的宽度。 | `undefined` | 可渲染节点 (继承) |
| `strokeCap` | `string` | 描边线端点的样式（例如，`'butt'`、`'round'`、`'square'`）。 | `undefined` | 可渲染节点 (继承) |
| `strokeJoin` | `string` | 描边连接角的样式（例如，`'miter'`、`'round'`、`'bevel'`）。 | `undefined` | 可渲染节点 (继承) |
| `strokeDash` | `array` | 一个指定描边虚线模式的数字数组（例如，`[5, 2]` 表示 5 个单位实线，2 个单位虚线）。 | `undefined` | 可渲染节点 (继承) |
| `opacity` | `number` | 文本的不透明度级别，从 0 到 1。 | `1` | 节点 (继承) |
| `visible` | `boolean` | 如果为 `false`，则文本将被隐藏。 | `true` | 节点 (继承) |
| `onClick` | `function` | 点击事件的处理程序。 | `undefined` | 节点 (继承) |
| `onMouseMove` | `function` | 鼠标移动事件的处理程序。 | `undefined` | 节点 (继承) |
| `onMouseOver` | `function` | 鼠标悬停事件的处理程序。 | `undefined` | 节点 (继承) |
| `onMouseOut` | `function` | 鼠标移出事件的处理程序。 | `undefined` | 节点 (继承) |
| `onMouseUp` | `function` | 鼠标抬起事件的处理程序。 | `undefined` | 节点 (继承) |
| `onMouseDown` | `function` | 鼠标按下事件的处理程序。 | `undefined` | 节点 (继承) |

## 使用示例

### 基本文本渲染

要显示简单文本，将字符串内容作为 `children` 提供：

```javascript
import *s React from 'react';
import { Surface, Group, Text } from 'react-art';

function BasicTextExample() {
  return (
    <Surface width={200} height={100}>
      <Group x={10} y={40}>
        <Text fill="blue">
          Hello, ART!
        </Text>
      </Group>
    </Surface>
  );
}

export default BasicTextExample;
```

此示例以蓝色渲染文本“Hello, ART!”。

### 字体样式和对齐

您可以使用 `font` 和 `alignment` 属性自定义文本的字体和对齐方式：

```javascript
import *s React from 'react';
import { Surface, Group, Text } from 'react-art';

function StyledTextExample() {
  return (
    <Surface width={300} height={200}>
      <Group x={10} y={20}>
        <Text 
          font="bold 24px 'Comic Sans MS'"
          fill="#FF5733"
        >
          Big Bold Text
        </Text>
      </Group>
      <Group x={150} y={80}>
        <Text 
          font={{fontSize: 16, fontFamily: 'Arial', fontWeight: 'normal'}}
          fill="#33FF57"
          alignment="center"
        >
          Centered Text
        </Text>
      </Group>
      <Group x={290} y={140}>
        <Text 
          font="italic 14px Verdana"
          fill="#3357FF"
          alignment="right"
        >
          Right-aligned Italic Text
        </Text>
      </Group>
    </Surface>
  );
}

export default StyledTextExample;
```

此示例演示了使用字符串和对象格式的 `font` 属性的不同字体样式和对齐方式。

### 沿路径的文本

对于更高级的文本布局，您可以沿自定义路径渲染文本。这需要从 `react-art` 导入 `Path` 并定义一个路径对象。

```javascript
import *s React from 'react';
import { Surface, Group, Text, Path } from 'react-art';

function PathTextExample() {
  // Define a simple curved path
  const curvedPath = new Path()
    .moveTo(20, 50)
    .curveTo(100, 10, 200, 90, 280, 50);

  return (
    <Surface width={300} height={150}>
      <Group>
        <Text 
          font="16px Georgia"
          fill="purple"
          path={curvedPath}
        >
          Text along a curved path
        </Text>
      </Group>
    </Surface>
  );
}

export default PathTextExample;
```

此示例展示了如何使文本沿着自定义 `Path` 移动，这对于徽标、装饰元素或复杂的数据可视化非常有用。

---

本节详细介绍了 `Text` 组件，涵盖了其属性和各种使用场景，从基本显示到高级路径跟随。现在，您可以有效地将文本信息整合到您的 React ART 图形中。要探索如何应用各种填充和描边，或变换您的元素，请继续阅读[样式和交互性](./styling-interactivity.md)部分。
