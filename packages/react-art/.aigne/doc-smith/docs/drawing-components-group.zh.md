# 组合

React ART 中的 `Group` 组件用作其他 ART 元素的容器，类似于 HTML 中的 `div`。您可以使用它将多个形状、文本，甚至其他组合组织成一个单一的逻辑单元。将元素分组可以使您同时对其所有子元素应用常见的转换，例如平移、旋转、缩放和不透明度，从而简化复杂的图形布局。有关转换的更多详细信息，请参阅[转换](./styling-interactivity-transformations.md)部分。

## 核心功能

`Group` 组件有助于结构组织，并简化共享属性和转换的应用。当您将 `x`、`y`、`rotation` 或 `opacity` 等属性应用于 `Group` 时，这些属性会继承并应用于该组合中嵌套的所有元素。这提供了一种有效的方式来管理多个相关图形组件的位置和外观。

`Group` 还支持 `width` 和 `height` 属性，它们定义了组合概念性包围盒的尺寸。这些属性可用于布局和对齐目的。

## 属性

`Group` 组件接受一系列属性，其中许多属性是大多数 ART 节点通用的。这些属性控制其位置、转换、可见性和交互性。附加到 `Group` 的事件监听器也将应用于其子元素，从而实现统一的交互处理。

| Name | Type | Description |
|---|---|---|
| `x` | `number` | 组合在像素中的水平平移。 |
| `y` | `number` | 组合在像素中的垂直平移。 |
| `rotation` | `number` | 组合的旋转角度（度）。 |
| `scale` | `number` | X 轴和 Y 轴的统一缩放因子。 |
| `scaleX` | `number` | 沿 X 轴的缩放因子。 |
| `scaleY` | `number` | 沿 Y 轴的缩放因子。 |
| `originX` | `number` | 旋转和缩放原点的 X 坐标。 |
| `originY` | `number` | 旋转和缩放原点的 Y 坐标。 |
| `transform` | `Transform` | 用于自定义矩阵转换的 ART `Transform` 对象。 |
| `width` | `number` | 组合包围盒的宽度。 |
| `height` | `number` | 组合包围盒的高度。 |
| `opacity` | `number` | 组合的透明度，值介于 0（完全透明）和 1（完全不透明）之间。 |
| `visible` | `boolean` | 控制组合及其子元素的可见性。设置为 `false` 可隐藏。 |
| `cursor` | `string` | 鼠标悬停在组合上时设置光标样式。 |
| `title` | `string` | 为组合设置工具提示标题。 |
| `onClick` | `function` | 点击事件的事件处理程序。 |
| `onMouseMove` | `function` | 鼠标移动事件的事件处理程序。 |
| `onMouseOver` | `function` | 鼠标进入事件的事件处理程序。 |
| `onMouseOut` | `function` | 鼠标离开事件的事件处理程序。 |
| `onMouseUp` | `function` | 鼠标抬起事件的事件处理程序。 |
| `onMouseDown` | `function` | 鼠标按下事件的事件处理程序。 |

## 使用示例

以下是演示如何使用 `Group` 组件包含和转换多个 `Shape` 元素的示例。请注意，将 `x`、`y` 和 `rotation` 应用于 `Group` 如何影响两个嵌套的形状。

```javascript
import React from 'react';
import { Surface, Group, Shape } from 'react-art';

function GroupExample() {
  return (
    <Surface width={300} height={200}>
      {/* A group transformed and rotated */}
      <Group x={50} y={50} rotation={30} opacity={0.8}>
        {/* First shape, positioned relative to the group's origin */}
        <Shape
          d="M0,0 L80,0 L80,50 L0,50 Z"
          fill="#FF5733"
          stroke="#C70039"
          strokeWidth={2}
        />

        {/* Second shape, also affected by the group's transformation */}
        <Shape
          d="M100,20 L150,20 L150,70 L100,70 Z"
          fill="#33FF57"
          stroke="#00C739"
          strokeWidth={2}
        />
      </Group>
    </Surface>
  );
}

export default GroupExample;
```

在此示例中，橙色和绿色矩形都是 `Group` 的子元素。`Group` 被平移 (50, 50) 像素并旋转 30 度，导致两个矩形相对于 `Surface` 整体定位和旋转。

---

`Group` 组件对于构建有组织且易于管理的图形至关重要。它简化了转换和属性继承，这对于创建复杂、动态的插图至关重要。接下来，探索如何使用[剪裁矩形](./drawing-components-clipping-rectangle.md)来限制 ART 元素的可见区域。