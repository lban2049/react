# 绘制组件

React ART 提供了一系列预构建组件，可简化绘制常见矢量图形的过程。这些组件旨在 `<Surface>` 元素内使用，并与 React 的声明式方法集成，允许您使用 props 定义图形。您可以将它们视为视觉场景的基本构建块。

要了解这些组件如何融入更广泛的 React ART 生态系统，您可能需要查阅[与 React 集成](./core-concepts-integration-with-react.md)和[渲染模式](./core-concepts-rendering-modes.md)部分。

## 可用绘制组件

下面列出的每个组件都带有一组自己的属性，用于控制其外观、位置和行为。单击链接可查看每个组件的详细文档和示例。

| 组件 | 描述 | 查看详情 |
|---|---|---|
| `Surface` | 所有 React ART 元素的基本根容器，定义了绘图区域。 | [Surface](./drawing-components-surface.md) |
| `Shape` | 一种多功能组件，使用 `d` 属性渲染自定义矢量路径，提供灵活的绘图能力。 | [Shape](./drawing-components-shape.md) |
| `Group` | 用于将多个 ART 元素组合在一起，实现共享变换和对形状集合的属性应用。 | [Group](./drawing-components-group.md) |
| `ClippingRectangle` | 定义一个矩形区域来裁剪内容。放置在 `ClippingRectangle` 内的任何 ART 元素将仅在其边界内可见。 | [Clipping Rectangle](./drawing-components-clipping-rectangle.md) |
| `Text` | 在您的 ART 图形中渲染文本，提供字体样式、对齐和定位的控制。 | [Text](./drawing-components-text.md) |
| `Circle` | 绘制完美的圆形，具有半径、描边和填充属性。 | [Circle](./drawing-components-circle.md) |
| `Rectangle` | 渲染矩形，具有可配置的宽度、高度和用于圆角的 `border-radius` 属性。 | [Rectangle](./drawing-components-rectangle.md) |
| `Wedge` | 创建弧线、部分圆形或完整圆形，精确控制起始/结束角度和内外半径。 | [Wedge](./drawing-components-wedge.md) |

---

本节介绍了 React ART 中可用的主要绘制组件。利用这些基本构建块，您可以构建各种矢量图形。接下来，您将在[样式和交互性](./styling-interactivity.md)部分学习如何应用样式（例如填充、描边和变换）以及如何处理这些组件上的用户交互。