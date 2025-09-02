# Rectangle

The `Rectangle` component in React ART allows you to draw rectangular shapes within your `Surface`. It supports basic width and height properties, as well as options for rounded corners using a uniform `radius` or individual corner radii.

To understand how `Rectangle` fits into the overall drawing process, you may want to refer to the [Shape](./drawing-components-shape.md) and [Surface](./drawing-components-surface.md) documentation.

## Basic Usage

You can define a simple rectangle by specifying its `width` and `height`. Like other ART components, you can apply `stroke` for borders and `fill` for the interior color.

```jsx
import React from 'react';
import { Surface, Rectangle } from 'react-art';

function MyDrawing() {
  return (
    <Surface width={200} height={200}>
      <Rectangle
        width={100}
        height={50}
        stroke="red"
        fill="lightcoral"
      />
    </Surface>
  );
}

export default MyDrawing;
```

This example creates a red-stroked, lightcoral-filled rectangle with a width of 100 units and a height of 50 units.

## Properties

The `Rectangle` component accepts the following properties specific to its shape, in addition to common [Shape](./drawing-components-shape.md) properties like `fill`, `stroke`, and `transform`.

| Name | Type | Description |
|---|---|---|
| `width` | `number` | The width of the rectangle. Negative values will offset the rectangle in the negative X direction. |
| `height` | `number` | The height of the rectangle. Negative values will offset the rectangle in the negative Y direction. |
| `radius` | `number` | An optional uniform radius to apply to all four corners of the rectangle. If individual corner radii are specified (e.g., `radiusTopLeft`), they will override this property for that specific corner. Negative values are treated as 0. |
| `radiusTopLeft` | `number` | An optional radius for the top-left corner. Overrides the `radius` property for this corner. Negative values are treated as 0. |
| `radiusTopRight` | `number` | An optional radius for the top-right corner. Overrides the `radius` property for this corner. Negative values are treated as 0. |
| `radiusBottomLeft` | `number` | An optional radius for the bottom-left corner. Overrides the `radius` property for this corner. Negative values are treated as 0. |
| `radiusBottomRight` | `number` | An optional radius for the bottom-right corner. Overrides the `radius` property for this corner. Negative values are treated as 0. |

## Example with Rounded Corners

You can create rectangles with rounded corners by using the `radius` or specific corner `radius` properties. Note that if the combined radii for a side exceed the `width` or `height`, the radii for that side will default to 0 to ensure the shape is valid.

```jsx
import React from 'react';
import { Surface, Rectangle } from 'react-art';

function RoundedRectangles() {
  return (
    <Surface width={300} height={200}>
      {/* Rectangle with uniform radius */}
      <Rectangle
        x={20} y={20} // Position the rectangle
        width={80}
        height={80}
        radius={15}
        stroke="blue"
        fill="lightblue"
      />

      {/* Rectangle with different corner radii */}
      <Rectangle
        x={150} y={20}
        width={120}
        height={100}
        radiusTopLeft={30}
        radiusBottomRight={50}
        stroke="green"
        fill="lightgreen"
      />
    </Surface>
  );
}

export default RoundedRectangles;
```

This example demonstrates two rectangles: one with a uniform radius on all corners, and another with distinct radii applied to its top-left and bottom-right corners.

---

Now that you understand how to draw rectangles, you might want to explore the [Wedge](./drawing-components-wedge.md) component for drawing arcs and partial shapes, or return to the [Drawing Components](./drawing-components.md) overview.