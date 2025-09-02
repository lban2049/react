# Styling and Interactivity

React ART enables you to create dynamic and visually rich graphics by providing robust mechanisms for styling and handling user interactions. This section introduces the core concepts for applying visual attributes like colors, gradients, and transformations, as well as responding to user input.

For in-depth details on each topic, refer to the dedicated sub-sections:

*   [Fills and Strokes](./styling-interactivity-fills-strokes.md)
*   [Transformations](./styling-interactivity-transformations.md)
*   [Event Handling](./styling-interactivity-event-handling.md)

## Fills and Strokes

React ART components that represent renderable shapes (like `Shape` or `Text`) support `fill` and `stroke` properties to control their appearance. The `fill` property determines the color or pattern inside a shape, while `stroke` defines the color, width, and style of its border.

You can use solid colors directly, or leverage declarative fill-type objects such as `LinearGradient`, `RadialGradient`, and `Pattern` for more complex visual effects. The `stroke` property also allows fine-tuning with `strokeWidth`, `strokeCap`, `strokeJoin`, and `strokeDash`.

Learn more about applying various fills and strokes in [Fills and Strokes](./styling-interactivity-fills-strokes.md).

## Transformations

To position, orient, and scale your graphic elements, React ART provides comprehensive transformation capabilities. You can apply translations (`x`, `y`), rotations (`rotation`), and scaling (`scale`, `scaleX`, `scaleY`) to individual components or groups of elements.

Complex transformations can be combined and managed using the `Transform` object, offering precise control over the visual layout of your ART scene.

Explore how to apply and combine transformations in [Transformations](./styling-interactivity-transformations.md).

## Event Handling

React ART elements are interactive, allowing you to capture user input such as clicks, mouse movements, and hovers. Similar to standard React components, you can attach event listeners directly as props to your ART components.

Supported event types include `onClick`, `onMouseMove`, `onMouseOver`, `onMouseOut`, `onMouseUp`, and `onMouseDown`. These events enable you to build dynamic and responsive graphics that react to user interactions.

Find detailed information on implementing interactivity in [Event Handling](./styling-interactivity-event-handling.md).

---

This section provided a high-level overview of styling and interactivity in React ART. By mastering fills, strokes, transformations, and event handling, you can create sophisticated and responsive graphic applications. For a comprehensive list of all components and their properties, proceed to the [API Reference](./api-reference.md).