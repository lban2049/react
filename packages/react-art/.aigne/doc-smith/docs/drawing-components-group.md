# Group

The `Group` component in React ART serves as a container for other ART elements, similar to a `div` in HTML. You can use it to organize multiple shapes, text, or even other groups into a single logical unit. Grouping elements allows you to apply common transformations such as translation, rotation, scaling, and opacity to all its children simultaneously, simplifying complex graphic layouts. For more details on transformations, see the [Transformations](./styling-interactivity-transformations.md) section.

## Core Functionality

The `Group` component facilitates structural organization and simplifies the application of shared properties and transformations. When you apply properties like `x`, `y`, `rotation`, or `opacity` to a `Group`, these properties are inherited and applied to all elements nested within that group. This provides an efficient way to manage the position and appearance of multiple related graphic components.

The `Group` also supports `width` and `height` properties, which define the dimensions of the group's conceptual bounding box. These can be useful for layout and alignment purposes.

## Properties

The `Group` component accepts a range of properties, many of which are common to most ART nodes. These properties control its position, transformation, visibility, and interactivity. Event listeners attached to a `Group` will also apply to its children, allowing for unified interaction handling.

| Name | Type | Description |
|---|---|---|
| `x` | `number` | The horizontal translation of the group in pixels. |
| `y` | `number` | The vertical translation of the group in pixels. |
| `rotation` | `number` | The rotation angle of the group in degrees. |
| `scale` | `number` | Uniform scaling factor for both X and Y axes. |
| `scaleX` | `number` | Scaling factor along the X-axis. |
| `scaleY` | `number` | Scaling factor along the Y-axis. |
| `originX` | `number` | The X-coordinate for the origin of rotation and scaling. |
| `originY` | `number` | The Y-coordinate for the origin of rotation and scaling. |
| `transform` | `Transform` | An ART `Transform` object for custom matrix transformations. |
| `width` | `number` | The width of the group's bounding box. |
| `height` | `number` | The height of the group's bounding box. |
| `opacity` | `number` | The transparency of the group, a value between 0 (fully transparent) and 1 (fully opaque). |
| `visible` | `boolean` | Controls the visibility of the group and its children. Set to `false` to hide. |
| `cursor` | `string` | Sets the cursor style when hovering over the group. |
| `title` | `string` | Sets a tooltip title for the group. |
| `onClick` | `function` | Event handler for click events. |
| `onMouseMove` | `function` | Event handler for mouse movement events. |
| `onMouseOver` | `function` | Event handler for mouse enter events. |
| `onMouseOut` | `function` | Event handler for mouse leave events. |
| `onMouseUp` | `function` | Event handler for mouse up events. |
| `onMouseDown` | `function` | Event handler for mouse down events. |

## Usage Example

Here’s an example demonstrating how to use the `Group` component to contain and transform multiple `Shape` elements. Notice how applying `x`, `y`, and `rotation` to the `Group` affects both nested shapes.

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

In this example, both the orange and green rectangles are children of the `Group`. The `Group` is translated by (50, 50) pixels and rotated by 30 degrees, causing both rectangles to be positioned and rotated collectively relative to the `Surface`.

---

The `Group` component is essential for building organized and easily manageable graphics. It simplifies transformations and property inheritance, which is crucial for creating complex, dynamic illustrations. Next, explore how to use the [Clipping Rectangle](./drawing-components-clipping-rectangle.md) to restrict the visible area of your ART elements.