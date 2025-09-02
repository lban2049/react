# Drawing Components

React ART provides a set of pre-built components that simplify the process of drawing common vector graphics. These components are designed to be used within a `<Surface>` element and integrate with React's declarative approach, allowing you to define your graphics with props. You can think of them as the basic building blocks for your visual scenes.

To understand how these components fit into the broader React ART ecosystem, you may want to review the [Integration with React](./core-concepts-integration-with-react.md) and [Rendering Modes](./core-concepts-rendering-modes.md) sections.

## Available Drawing Components

Each component listed below comes with its own set of properties to control its appearance, position, and behavior. Click on the links to explore detailed documentation and examples for each.

| Component | Description | See Details |
|---|---|---|
| `Surface` | The essential root container for all React ART elements, defining the drawing area. | [Surface](./drawing-components-surface.md) |
| `Shape` | A versatile component for rendering custom vector paths using the `d` prop, providing flexible drawing capabilities. | [Shape](./drawing-components-shape.md) |
| `Group` | Used to group multiple ART elements together, enabling shared transformations and property applications for a collection of shapes. | [Group](./drawing-components-group.md) |
| `ClippingRectangle` | Defines a rectangular area to clip content. Any ART elements placed within a `ClippingRectangle` will only be visible inside its bounds. | [Clipping Rectangle](./drawing-components-clipping-rectangle.md) |
| `Text` | Renders text within your ART graphics, offering control over font styling, alignment, and positioning. | [Text](./drawing-components-text.md) |
| `Circle` | Draws perfect circles with properties for radius, stroke, and fill. | [Circle](./drawing-components-circle.md) |
| `Rectangle` | Renders rectangles with configurable width, height, and border-radius properties for rounded corners. | [Rectangle](./drawing-components-rectangle.md) |
| `Wedge` | Creates arcs, partial circles, or full circular shapes, with precise control over start/end angles and inner/outer radii. | [Wedge](./drawing-components-wedge.md) |

---

This section introduced the primary drawing components available in React ART. With these building blocks, you can construct a wide range of vector graphics. Next, you will learn how to apply styling, such as fills, strokes, and transformations, and how to handle user interactions on these components in the [Styling and Interactivity](./styling-interactivity.md) section.