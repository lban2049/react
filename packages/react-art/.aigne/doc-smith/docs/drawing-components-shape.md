# Shape

The `Shape` component in React ART provides a versatile way to draw custom vector graphics using a path definition. It allows you to create complex geometric forms beyond basic circles or rectangles by defining a series of drawing commands. This component is fundamental for rendering custom illustrations and visual elements.

For an overview of all drawing components, refer to the [Drawing Components](./drawing-components.md) section. To understand how to apply colors and outlines, see [Fills and Strokes](./styling-interactivity-fills-strokes.md).

## Custom Path Data

The core of the `Shape` component is its ability to interpret path data, which dictates the form it takes. You provide this path data through either the `d` property or as children of the `Shape` component.

*   **`d` Property**: This property accepts a string that represents the path commands. These commands are similar to SVG path data, defining movements (e.g., `M` for moveTo), lines (e.g., `L` for lineTo), curves (e.g., `C` for cubic Bézier), and arcs.
*   **Children**: Alternatively, you can provide the path data as string children directly within the `Shape` component. If multiple children are provided, they are joined together to form the complete path string.

The `Shape` component uses this path data to draw the outline of your graphic, which can then be filled and stroked.

## Properties

Beyond the path definition, the `Shape` component accepts various properties to control its appearance, position, and interactivity. Many of these properties are inherited from general ART node properties, affecting how the shape is rendered and transformed.

| Name | Type | Description |
|---|---|---|
| `d` | `string` | The path data string defining the shape's geometry. For example, `"M10 10 L100 10 L100 100 L10 100 Z"` for a square. |
| `children` | `string` or `Array<string>` | Alternative way to provide path data. String children are concatenated to form the path. |
| `width` | `number` | The width of the shape's bounding box. |
| `height` | `number` | The height of the shape's bounding box. |
| `fill` | `string` or `object` | Defines the fill color or gradient/pattern object for the shape. Can be a color string (e.g., `"red"`) or an instance of `LinearGradient`, `RadialGradient`, or `Pattern`. |
| `stroke` | `string` | Defines the stroke (outline) color of the shape. |
| `strokeWidth` | `number` | The width of the stroke. |
| `strokeCap` | `string` | The style of the caps at the ends of open subpaths (e.g., `"butt"`, `"round"`, `"square"`). |
| `strokeJoin` | `string` | The style of the corners (e.g., `"miter"`, `"round"`, `"bevel"`). |
| `strokeDash` | `Array<number>` | An array of numbers specifying the lengths of alternating dashes and gaps for the stroke. |
| `x` | `number` | The x-coordinate for the shape's translation. |
| `y` | `number` | The y-coordinate for the shape's translation. |
| `rotation` | `number` | The rotation angle in degrees. |
| `originX` | `number` | The x-coordinate for the center of rotation and scaling. |
| `originY` | `number` | The y-coordinate for the center of rotation and scaling. |
| `scale` | `number` | Uniform scaling factor. |
| `scaleX` | `number` | Scaling factor along the x-axis. |
| `scaleY` | `number` | Scaling factor along the y-axis. |
| `transform` | `object` | An ART `Transform` object for custom transformations. |
| `opacity` | `number` | The opacity of the shape (0 to 1). |
| `visible` | `boolean` | Controls the visibility of the shape. `true` (default) for visible, `false` for hidden. |
| `cursor` | `string` | The cursor style when hovering over the shape (e.g., `"pointer"`). |
| `title` | `string` | A tooltip title for the shape. |
| `onClick` | `function` | Event handler for click events. |
| `onMouseMove` | `function` | Event handler for mouse move events. |
| `onMouseOver` | `function` | Event handler for mouse over events. |
| `onMouseOut` | `function` | Event handler for mouse out events. |
| `onMouseUp` | `function` | Event handler for mouse up events. |
| `onMouseDown` | `function` | Event handler for mouse down events. |

## Usage Example

This example demonstrates how to draw a simple triangular `Shape` component using the `d` property, applying a solid fill color and a stroke.

```javascript
import React from 'react';
import { Surface, Shape } from 'react-art';

function TriangleArt() {
  return (
    <Surface width={200} height={200}>
      {/* Defines a path for a triangle */}
      <Shape
        d="M100 10 L190 190 L10 190 Z"
        fill="blue"
        stroke="darkblue"
        strokeWidth={5}
      />
    </Surface>
  );
}

export default TriangleArt;
```

This code renders a blue triangle with a dark blue outline on a 200x200 pixel surface. The `d` prop specifies the path: `M100 10` moves to (100, 10), `L190 190` draws a line to (190, 190), `L10 190` draws a line to (10, 190), and `Z` closes the path back to the starting point.

## Conclusion

The `Shape` component is essential for creating custom, complex vector graphics in React ART. By mastering its path data and styling properties, you can draw virtually any form required for your application's visuals.

To learn more about grouping elements for easier transformations and management, proceed to the [Group](./drawing-components-group.md) section.