# 故障排除和支持

本节提供了在使用 React ART 时可能遇到的常见问题的指导，概述了基本的调试技术，并指出了获取帮助和报告错误的资源。

## 常见问题和解决方案

在使用 React ART 进行开发时，您可能会遇到特定的错误或意外的渲染行为。了解常见的陷阱有助于快速解决它们。

### 不支持的组件类型

React ART 支持一组特定的绘图基元，这些基元直接映射自底层的 ART 库。尝试使用不支持的类型将导致错误：

```
Error: ReactART does not support the type "[YOUR_TYPE]"
```

此错误表明您正在尝试渲染一种 React ART 内部实例创建逻辑无法识别的组件类型。支持的类型包括 `ClippingRectangle`、`Group`、`Shape` 和 `Text`。请确保您的组件名称与 React ART 中定义的这些类型匹配。

### 文本子元素扁平化

在渲染 `Text` 组件时，请确保文本内容已扁平化。如果您直接组合多个字符串或数字子元素，可能会遇到错误：

```
Error: Text children should already be flattened.
```

这意味着 `Text` 组件期望其子元素是一个单独的字符串或数字，而不是多个独立的文本节点。在将文本内容作为子元素传递给 `Text` 之前，请将其合并成一个字符串。

### 高级 React 功能（视图过渡、手势过渡）

React ART 在 React 生态系统中充当辅助渲染器。主渲染器（如 React DOM）中可用的某些高级功能（例如，视图过渡、手势过渡）在 React ART 中未明确实现或为空操作。尝试使用它们将不会产生预期效果，或可能引发错误：

```javascript
// 这些函数在 React ART 中为空操作或会抛出错误
startViewTransition(); // 返回 null
startGestureTransition(); // 抛出错误：“未实现。”
getCurrentGestureOffset(); // 抛出错误：“react-art 尚不支持 startGestureTransition。”
```

如果您需要这些特定功能，您可能需要考虑不同的渲染解决方案，或者在 React ART 之外实现自定义逻辑。

### 不正确的属性应用

渲染问题通常源于不正确的属性使用。例如，变换通过 `x`、`y`、`rotation`、`scale`、`scaleX`、`scaleY`、`originX`、`originY` 以及 `transform` 属性来应用。如果这些属性应用不当，可能会出现视觉不一致。

诸如 `opacity`、`visible`、`fill`、`stroke`、`strokeWidth`、`strokeCap`、`strokeJoin` 和 `strokeDash` 等属性也控制视觉方面。请始终查阅 [API 参考](./api-reference.md)以了解每种组件类型（例如 `Shape`、`Text`）的正确属性及其预期值。

## 调试技术

调试 React ART 应用程序涉及标准 React 调试实践和检查目标环境（Canvas、SVG 或 VML）中渲染输出的结合。

*   **浏览器开发者工具**：对于 Canvas、SVG 或 VML 输出，请使用浏览器的开发者工具检查生成的元素。对于 SVG，您可以直接查看 SVG DOM 结构。对于 Canvas，您通常可以检查 Canvas 元素本身并检查渲染上下文错误。
*   **控制台日志记录**：记录传递给 ART 组件的 `props` 以及由 React ART 渲染器创建的 `instance` 对象（`createInstance` 或 `getPublicInstance` 的返回值）。这有助于验证您的数据是否正确流向 ART 基元。
*   **事件监听器检查**：如果事件未触发，请验证当提供 `onClick` 或 `onMouseMove` 等属性时，`addEventListeners` 和 `destroyEventListeners` 是否在内部被正确调用。

## 获取帮助和报告错误

如果您遇到错误或需要进一步的帮助，可以利用以下资源：

*   **GitHub Issues**：要报告错误、提交功能请求或讨论特定的技术挑战，主要渠道是 [React GitHub issues 页面](https://github.com/facebook/react/issues)。在报告错误时，请提供清晰的描述、重现步骤、预期行为和实际行为。
*   **React 文档**：主要的 [React 文档主页](https://react.dev/) 是理解 React ART 核心 React 概念的宝贵资源。

## 后续步骤

本节提供了故障排除和寻求支持的基本指导。有关所有可用组件及其属性的详细信息，请参阅 [API 参考](./api-reference.md)部分。