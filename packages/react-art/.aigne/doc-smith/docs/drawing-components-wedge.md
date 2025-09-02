# Wedge

The `Wedge` component in React ART is used for drawing a variety of circular shapes, including full circles, partial arcs, and wedges. This component provides precise control over angles and radii, enabling you to create diverse graphic elements within a `<Surface>`.

To understand how `Wedge` integrates with the overall drawing system, you might find it helpful to review the documentation for the root container, [`Surface`](./drawing-components-surface.md), and the generic path drawing component, [`Shape`](./drawing-components-shape.md).

## How it Works

`Wedge` functions by calculating the necessary path data (the `d` attribute) for an ART `Shape` element. It takes your specified angles and radii, converts them into a series of drawing commands, and then renders a `Shape` with that calculated path. This abstraction allows you to declare the desired circular geometry without manually defining complex SVG path commands.

## Properties

The `Wedge` component accepts several properties to define its appearance and geometry:

| Name | Type | Description | Required | Default |
|---|---|---|---|---|
| `outerRadius` | `number` | The outer radius of the circle, arc, or wedge in pixels. | Yes | |
| `startAngle` | `number` | The starting angle in degrees, relative to 12 o'clock (0 degrees points straight up, increasing clockwise). | Yes | |
| `endAngle` | `number` | The ending angle in degrees, relative to 12 o'clock. If `endAngle` is equal to or greater than `startAngle + 360`, a full circle is drawn. | Yes | |
| `innerRadius` | `number` | The inner radius in pixels. If greater than `0`, the component draws a ring or an arc. If `0` or omitted, it draws a solid circle or a wedge. | No | `0` |
| `fill` | `string` | The color or gradient used to fill the wedge. Standard CSS color strings are accepted (e.g., `'blue'`, `'#FF0000'`, `'rgba(0,0,255,0.5)'`). | No | |

## Example Usage

Here’s an example demonstrating how to draw a blue wedge:

```javascript
import React from 'react';
import { Surface, Wedge } from 'react-art';

function MyWedgeComponent() {
  return (
    <Surface width={200} height={200}>
      <Wedge
        outerRadius={80}
        innerRadius={40}
        startAngle={0}
        endAngle={270}
        fill="blue"
      />
    </Surface>
  );
}

export default MyWedgeComponent;
```

This example renders a 200x200 pixel surface containing a blue wedge. The wedge has an outer radius of 80 pixels and an inner radius of 40 pixels, creating an arc. It starts at 0 degrees (12 o'clock) and extends clockwise to 270 degrees. If `innerRadius` were omitted or set to 0, it would draw a solid sector of a circle.

## Drawing a Full Circle

To draw a full circle or ring, set the `endAngle` to be equal to or greater than `startAngle + 360`.

```javascript
import React from 'react';
import { Surface, Wedge } from 'react-art';

function MyCircleComponent() {
  return (
    <Surface width={150} height={150}>
      {/* Solid red circle */}
      <Wedge
        outerRadius={70}
        startAngle={0}
        endAngle={360}
        fill="red"
      />
      {/* Green ring */}
      <Wedge
        outerRadius={60}
        innerRadius={50}
        startAngle={0}
        endAngle={720} // Can be any value >= startAngle + 360
        fill="green"
        // Position the ring so it's visible, for example by using a Group
        // or by adjusting Surface size/Wedge position if Surface is larger
      />
    </Surface>
  );
}

export default MyCircleComponent;
```

This example shows how to render a solid red circle and a green ring using the `Wedge` component by setting `endAngle` to `360` or `720` respectively.

---

Now that you understand how to draw arcs, circles, and wedges, you may want to explore other ways to enhance your graphics. Continue to the [Styling and Interactivity](./styling-interactivity.md) section to learn about applying fills, strokes, and transformations, or dive into the full [API Reference](./api-reference.md) for more details on `react-art` components and utilities.