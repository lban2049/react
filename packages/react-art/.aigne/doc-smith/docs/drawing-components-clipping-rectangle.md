# Clipping Rectangle

The `ClippingRectangle` component in React ART defines a rectangular region that clips its child elements. This means that any part of a child element that extends beyond the boundaries of the `ClippingRectangle` will not be rendered. This functionality is useful for creating complex layouts, masking content, or managing overflow within your ART graphics. It operates similarly to a viewport or a mask, containing its children within its defined bounds.

For an understanding of how `ClippingRectangle` fits into the broader structure of drawing elements, refer to the [Drawing Components](./drawing-components.md) overview. For general grouping and transformations without clipping, see the [Group](./drawing-components-group.md) component.

## Creating a ClippingRectangle

You create a `ClippingRectangle` by using the `ClippingRectangle` component exported from `react-art`. Internally, this component instantiates an ART `ClippingRectangle` object, which is then managed by React ART's reconciler.

## Properties

The `ClippingRectangle` component accepts specific properties to define its clipping area, and it also inherits a common set of properties from ART's base node type, allowing for positioning, transformations, and interactivity.

### Clipping-Specific Properties

These properties directly control the dimensions of the clipping area:

| Name | Type | Description |
|---|---|---|
| `width` | `number` | The width of the clipping rectangle. |
| `height` | `number` | The height of the clipping rectangle. |

### Inherited Node Properties

`ClippingRectangle` components also support the following properties, which are common to many ART drawing components, enabling control over position, appearance, and interactivity.

| Name | Type | Description |
|---|---|---|
| `x` | `number` | The x-coordinate for the position of the clipping rectangle. Default is `0`. |
| `y` | `number` | The y-coordinate for the position of the clipping rectangle. Default is `0`. |
| `rotation` | `number` | The rotation angle in degrees around the `originX` and `originY`. Default is `0`. |
| `originX` | `number` | The x-coordinate of the rotation origin. If not set, rotation occurs around the component's `x` position. |
| `originY` | `number` | The y-coordinate of the rotation origin. If not set, rotation occurs around the component's `y` position. |
| `scale` | `number` | Uniform scaling factor applied to both x and y axes around `originX` and `originY`. Default is `1`. |
| `scaleX` | `number` | Scaling factor applied to the x-axis. If `scale` is also set, `scaleX` takes precedence. Default is `1`. |
| `scaleY` | `number` | Scaling factor applied to the y-axis. If `scale` is also set, `scaleY` takes precedence. Default is `1`. |
| `transform` | `Transform` | An ART `Transform` object for custom transformation matrices. It is applied after `x`, `y`, `rotation`, and `scale`. |
| `opacity` | `number` | A value between `0` and `1` representing the opacity of the component. `0` is fully transparent, `1` is fully opaque. Default is `1`. |
| `visible` | `boolean` | Controls the visibility of the component. `true` (default) for visible, `false` for hidden. |
| `cursor` | `string` | Sets the mouse cursor style when hovering over the component (e.g., `'pointer'`, `'grab'`). |
| `title` | `string` | A title or tooltip text to display on hover. |
| `onClick` | `function` | Event handler for mouse click events. |
| `onMouseMove` | `function` | Event handler for mouse move events. |
| `onMouseOver` | `function` | Event handler for mouse over events. |
| `onMouseOut` | `function` | Event handler for mouse out events. |
| `onMouseUp` | `function` | Event handler for mouse up events. |
| `onMouseDown` | `function` | Event handler for mouse down events. |

## Example

Here is an example demonstrating how to use `ClippingRectangle` to clip a `Shape` component. In this scenario, only the portion of the red rectangle that falls within the `ClippingRectangle`'s `100x100` area will be visible.

```javascript
import React from 'react';
import { Surface, ClippingRectangle, Shape, Path } from 'react-art';

const ClippingExample = () => {
  const largeRectanglePath = Path()
    .rect(0, 0, 200, 200); // A 200x200 rectangle

  return (
    <Surface width={300} height={300}>
      {/* Clipping rectangle: only 100x100 area at (50,50) is visible */}
      <ClippingRectangle x={50} y={50} width={100} height={100}>
        {/* A large red shape, positioned at (0,0) relative to ClippingRectangle's origin */}
        <Shape 
          d={largeRectanglePath}
          fill="red"
          stroke="black"
          strokeWidth={2}
        />
      </ClippingRectangle>

      {/* Optional: Add a visual indicator of the clipping rectangle's bounds */}
      <Shape 
        d={Path().rect(50, 50, 100, 100)}
        stroke="blue"
        strokeWidth={1}
        opacity={0.5}
      />
    </Surface>
  );
};

export default ClippingExample;
```

In this example, the `ClippingRectangle` is positioned at `(50, 50)` and has dimensions `width={100}` and `height={100}`. The child `Shape` draws a 200x200 red rectangle starting at `(0,0)` relative to its parent `ClippingRectangle`. Therefore, only the top-left 100x100 portion of the red rectangle will be visible, effectively cropping the rest. The blue outline provides a visual reference for the clipping boundaries.

---

With `ClippingRectangle`, you can precisely control the visible areas of your ART elements, enabling cleaner layouts and specialized visual effects. To learn about rendering text within your ART graphics, proceed to the [Text](./drawing-components-text.md) section.