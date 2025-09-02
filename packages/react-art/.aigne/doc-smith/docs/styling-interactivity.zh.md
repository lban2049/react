# 样式和交互性

React ART 提供了强大的机制来设置样式和处理用户交互，使您能够创建动态且视觉丰富的图形。本节将介绍应用颜色、渐变和变换等视觉属性以及响应用户输入的核心概念。

有关每个主题的详细信息，请参阅以下专门的小节：

*   [填充和描边](./styling-interactivity-fills-strokes.md)
*   [变换](./styling-interactivity-transformations.md)
*   [事件处理](./styling-interactivity-event-handling.md)

## 填充和描边

代表可渲染形状（如 `Shape` 或 `Text`）的 React ART 组件支持 `fill` 和 `stroke` 属性来控制其外观。`fill` 属性决定了形状内部的颜色或图案，而 `stroke` 定义了其边框的颜色、宽度和样式。

您可以直接使用纯色，或者利用声明式填充类型对象，例如 `LinearGradient`、`RadialGradient` 和 `Pattern` 来实现更复杂的视觉效果。`stroke` 属性还允许通过 `strokeWidth`、`strokeCap`、`strokeJoin` 和 `strokeDash` 进行微调。

在[填充和描边](./styling-interactivity-fills-strokes.md)中了解更多关于应用各种填充和描边的信息。

## 变换

为了定位、定向和缩放图形元素，React ART 提供了全面的变换功能。您可以对单个组件或元素组应用平移（`x`、`y`）、旋转（`rotation`）和缩放（`scale`、`scaleX`、`scaleY`）。

复杂的变换可以通过 `Transform` 对象进行组合和管理，从而精确控制 ART 场景的视觉布局。

在[变换](./styling-interactivity-transformations.md)中探索如何应用和组合变换。

## 事件处理

React ART 元素是交互式的，允许您捕获用户输入，例如点击、鼠标移动和悬停。与标准 React 组件类似，您可以将事件监听器直接作为 props 附加到您的 ART 组件。

支持的事件类型包括 `onClick`、`onMouseMove`、`onMouseOver`、`onMouseOut`、`onMouseUp` 和 `onMouseDown`。这些事件使您能够构建动态且响应用户交互的图形。

在[事件处理](./styling-interactivity-event-handling.md)中查找有关实现交互性的详细信息。

---

本节提供了 React ART 中样式和交互性的高级概述。通过掌握填充、描边、变换和事件处理，您可以创建复杂且响应迅速的图形应用程序。有关所有组件及其属性的完整列表，请继续查阅[API 参考](./api-reference.md)。