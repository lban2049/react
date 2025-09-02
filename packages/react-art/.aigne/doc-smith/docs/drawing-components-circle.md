# Circle

The `Circle` component in React ART provides a straightforward way to draw circular shapes. Like other drawing components, it must be rendered within a [`Surface`](./drawing-components-surface.md) component. The `Circle` component simplifies the process of creating a circular path by abstracting the underlying `Path` creation, allowing you to focus on the circle's visual properties.

For a deeper understanding of how shapes are constructed and styled, refer to the [`Shape`](./drawing-components-shape.md) documentation.

## Usage

To use the `Circle` component, you typically specify its `radius` and optionally provide styling properties such as `stroke`, `strokeWidth`, and `fill`. These styling properties are passed down to the internal `Shape` component that `Circle` renders.

```jsx
import { Surface, Circle } from 'react-art';

function MyCircleGraphic() {
  return (
    <Surface width={200} height={200}>
      <Circle
        x={100}
        y={100}
        radius={50}
        stroke="#FF6347" // Tomato red outline
        strokeWidth={5}
        fill="#6A5ACD"   // Slate blue fill
      />
    </Surface>
  );
}

export default MyCircleGraphic;
```

This example renders a circle centered at `(100, 100)` with a radius of 50 units, a red outline, a specified outline width, and a blue fill.

## Properties

The `Circle` component accepts the following specific property, in addition to all properties supported by the generic [`Shape`](./drawing-components-shape.md) component.

| Name | Type | Description |
|---|---|---|
| `radius` | `number` | The radius of the circle in logical units. This is a required property. |

Commonly used `Shape` properties that you can pass to `Circle` include:

| Name | Type | Description |
|---|---|---|
| `x` | `number` | The x-coordinate for the circle's center relative to its parent `Group` or `Surface`. |
| `y` | `number` | The y-coordinate for the circle's center relative to its parent `Group` or `Surface`. |
| `fill` | `string` or `ARTGradient` or `ARTPattern` | The color or pattern to fill the circle. Can be a color string (e.g., "red", "#RRGGBB"), a gradient, or a pattern. |
| `stroke` | `string` or `ARTGradient` | The color or gradient for the circle's outline. |
| `strokeWidth` | `number` | The width of the circle's outline. |
| `opacity` | `number` | The transparency of the circle (0.0 to 1.0). |
| `transform` | `ARTTransform` | An `ARTTransform` object to apply transformations like translation, rotation, and scaling. |

## Example

Here is an example demonstrating a `Circle` component with various properties and within a `Group` for applying transformations.

```jsx
import React from 'react';
import { Surface, Group, Circle, Transform } from 'react-art';

function AnimatedCircle() {
  const [rotation, setRotation] = React.useState(0);

  React.useEffect(() => {
    const interval = setInterval(() => {
      setRotation(prevRotation => (prevRotation + 1) % 360);
    }, 20);
    return () => clearInterval(interval);
  }, []);

  const circleTransform = new Transform().rotate(rotation, 50, 50);

  return (
    <Surface width={300} height={300}>
      <Group x={100} y={100}> {/* Offset the group to center the circle */}
        <Circle
          radius={40}
          stroke="darkgreen"
          strokeWidth={2}
          fill="lightgreen"
          transform={circleTransform}
        />
      </Group>
      <Group x={200} y={100}> {/* Another circle for comparison */}
        <Circle
          radius={30}
          stroke="purple"
          strokeWidth={3}
          fill="pink"
        />
      </Group>
    </Surface>
  );
}

export default AnimatedCircle;
```

This example creates an animated circle that continuously rotates around its center point. It also includes a static second circle for comparison. The `Transform` object is used to apply the rotation, demonstrating how you can control visual effects dynamically.

---

This section detailed the `Circle` component, including its usage and properties for drawing circular shapes. You can now create basic circular elements in your React ART graphics. Next, you might explore the [`Rectangle`](./drawing-components-rectangle.md) component for drawing rectangular shapes or delve into [Styling and Interactivity](./styling-interactivity.md) to enhance your graphics.