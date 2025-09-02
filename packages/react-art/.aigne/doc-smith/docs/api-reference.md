# API Reference

This section provides a comprehensive reference for all public APIs exposed by `react-art`. You will find detailed descriptions of components, utility classes, and their properties, facilitating in-depth usage of the library. For an introductory guide to using these components in practice, refer to the [Drawing Components](./drawing-components.md) section. To understand how to apply styling and handle interactions, consult [Styling and Interactivity](./styling-interactivity.md). For details on internal types and event mechanisms, see [Internal Types and Events](./core-concepts-internal-types-events.md).

## Components

### Surface

The `Surface` component is the root container for all ART elements. It defines the drawing area's dimensions and serves as the canvas where all other ART components are rendered. Internally, it manages the ART surface instance and handles updates to its dimensions.

**Props**

| Name | Type | Description |
|:---|:---|:---|
| `height` | `number` | The height of the drawing surface. Required to set the canvas or SVG element's height. |
| `width` | `number` | The width of the drawing surface. Required to set the canvas or SVG element's width. |
| `accessKey` | `string` | Standard HTML `accessKey` attribute for accessibility. |
| `className` | `string` | Standard HTML `className` attribute for styling the root element. |
| `draggable` | `boolean` | Standard HTML `draggable` attribute. |
| `role` | `string` | Standard HTML `role` attribute for ARIA accessibility. |
| `style` | `object` | Standard React `style` object for inline CSS. |
| `tabIndex` | `number` | Standard HTML `tabIndex` attribute. |
| `title` | `string` | Standard HTML `title` attribute, often displayed as a tooltip. |

**Usage Example**

```jsx
import { Surface, Shape } from 'react-art';

function MyDrawing() {
  return (
    <Surface width={300} height={200} style={{ border: '1px solid black' }}>
      <Shape d="M0 0 L100 0 L50 100 Z" fill="blue" x={50} y={50} />
    </Surface>
  );
}
```

This example creates a 300x200 pixel drawing surface with a border and renders a blue triangle inside it.

### ClippingRectangle

The `ClippingRectangle` component defines a rectangular clipping region. Any ART elements rendered as children within a `ClippingRectangle` will only be visible within this defined region.

**Props**

| Name | Type | Description |
|:---|:---|:---|
| `width` | `number` | The width of the clipping rectangle. |
| `height` | `number` | The height of the clipping rectangle. |
| `x` | `number` | The x-coordinate of the clipping rectangle's top-left corner relative to its parent. Default is `0`. |
| `y` | `number` | The y-coordinate of the clipping rectangle's top-left corner relative to its parent. Default is `0`. |
| `rotation` | `number` | Rotation angle in degrees. Default is `0`. |
| `originX` | `number` | The x-coordinate of the rotation and scale origin. Defaults to the component's center. |
| `originY` | `number` | The y-coordinate of the rotation and scale origin. Defaults to the component's center. |
| `scale` | `number` | Uniform scale factor for both X and Y axes. Default is `1`. |
| `scaleX` | `number` | Scale factor for the X-axis. Default is `1`. |
| `scaleY` | `number` | Scale factor for the Y-axis. Default is `1`. |
| `transform` | `object` | An ART `Transform` object to apply complex transformations. Overrides `x`, `y`, `rotation`, `scale`, etc. |
| `cursor` | `string` | The CSS cursor style to apply when hovering over the component. |
| `title` | `string` | A title for the component, often displayed as a tooltip. |
| `opacity` | `number` | The opacity of the component, from `0` (fully transparent) to `1` (fully opaque). Default is `1`. |
| `visible` | `boolean` | Controls the visibility of the component. If `false`, the component is hidden. Default is `true`. |
| `onClick` | `function` | Event handler for click events. |
| `onMouseMove` | `function` | Event handler for mouse move events. |
| `onMouseOver` | `function` | Event handler for mouse over events. |
| `onMouseOut` | `function` | Event handler for mouse out events. |
| `onMouseUp` | `function` | Event handler for mouse up events. |
| `onMouseDown` | `function` | Event handler for mouse down events. |

**Usage Example**

```jsx
import { Surface, Group, ClippingRectangle, Shape } from 'react-art';

function ClippedContent() {
  return (
    <Surface width={200} height={200}>
      <ClippingRectangle x={50} y={50} width={100} height={100}>
        <Group x={-25} y={-25}> {/* Move group to see clipping effect */}
          <Shape d="M0 0 L150 0 L75 150 Z" fill="red" />
        </Group>
      </ClippingRectangle>
    </Surface>
  );
}
```

This example draws a red triangle, but only the part within the 100x100 rectangle at (50, 50) on the surface will be visible.

### Group

The `Group` component is used to logically group multiple ART elements. Applying properties like transformations, opacity, or event handlers to a `Group` will affect all its children simultaneously. Groups can also define their own `width` and `height`, which can influence layout and scaling for their children.

**Props**

| Name | Type | Description |
|:---|:---|:---|
| `width` | `number` | The width of the group. |
| `height` | `number` | The height of the group. |
| `x` | `number` | The x-coordinate of the group's top-left corner relative to its parent. Default is `0`. |
| `y` | `number` | The y-coordinate of the group's top-left corner relative to its parent. Default is `0`. |
| `rotation` | `number` | Rotation angle in degrees. Default is `0`. |
| `originX` | `number` | The x-coordinate of the rotation and scale origin. Defaults to the component's center. |
| `originY` | `number` | The y-coordinate of the rotation and scale origin. Defaults to the component's center. |
| `scale` | `number` | Uniform scale factor for both X and Y axes. Default is `1`. |
| `scaleX` | `number` | Scale factor for the X-axis. Default is `1`. |
| `scaleY` | `number` | Scale factor for the Y-axis. Default is `1`. |
| `transform` | `object` | An ART `Transform` object to apply complex transformations. Overrides `x`, `y`, `rotation`, `scale`, etc. |
| `cursor` | `string` | The CSS cursor style to apply when hovering over the component. |
| `title` | `string` | A title for the component, often displayed as a tooltip. |
| `opacity` | `number` | The opacity of the component, from `0` (fully transparent) to `1` (fully opaque). Default is `1`. |
| `visible` | `boolean` | Controls the visibility of the component. If `false`, the component is hidden. Default is `true`. |
| `onClick` | `function` | Event handler for click events. |
| `onMouseMove` | `function` | Event handler for mouse move events. |
| `onMouseOver` | `function` | Event handler for mouse over events. |
| `onMouseOut` | `function` | Event handler for mouse out events. |
| `onMouseUp` | `function` | Event handler for mouse up events. |
| `onMouseDown` | `function` | Event handler for mouse down events. |

**Usage Example**

```jsx
import { Surface, Group, Shape } from 'react-art';

function GroupExample() {
  return (
    <Surface width={300} height={200}>
      <Group x={50} y={50} rotation={45}>
        <Shape d="M0 0 L50 0 L25 50 Z" fill="purple" />
        <Shape d="M60 0 L110 0 L85 50 Z" fill="orange" />
      </Group>
    </Surface>
  );
}
```

This example groups two shapes. The entire group is moved to (50, 50) and rotated by 45 degrees, affecting both shapes within it.

### Shape

The `Shape` component is the most versatile drawing primitive, allowing you to draw custom paths using SVG-like path data. It supports various fill and stroke properties.

**Props**

| Name | Type | Description |
|:---|:---|:---|
| `d` | `string` | The SVG-like path data string (e.g., `"M10 10 L20 20 Z"`). Can also be passed as `children`. |
| `children` | `string` | Alternative to `d` for providing path data. Multiple children strings are joined. |
| `width` | `number` | The width of the shape. Used with `d` for scaling. |
| `height` | `number` | The height of the shape. Used with `d` for scaling. |
| `fill` | `string` or `object` | The fill color (e.g., `"#FF0000"`) or a fill object (`LinearGradient`, `RadialGradient`, `Pattern`). |
| `stroke` | `string` | The stroke color. |
| `strokeWidth` | `number` | The width of the stroke. |
| `strokeCap` | `string` | Specifies the shape to be used at the end of open subpaths when they are stroked. Values: `butt`, `round`, `square`. |
| `strokeJoin` | `string` | Specifies the shape to be used at the corners of paths when they are stroked. Values: `miter`, `round`, `bevel`. |
| `strokeDash` | `array` | An array of numbers specifying the lengths of alternating dashes and gaps for the stroke. |
| `x` | `number` | The x-coordinate of the shape's top-left corner relative to its parent. Default is `0`. |
| `y` | `number` | The y-coordinate of the shape's top-left corner relative to its parent. Default is `0`. |
| `rotation` | `number` | Rotation angle in degrees. Default is `0`. |
| `originX` | `number` | The x-coordinate of the rotation and scale origin. Defaults to the component's center. |
| `originY` | `number` | The y-coordinate of the rotation and scale origin. Defaults to the component's center. |
| `scale` | `number` | Uniform scale factor for both X and Y axes. Default is `1`. |
| `scaleX` | `number` | Scale factor for the X-axis. Default is `1`. |
| `scaleY` | `number` | Scale factor for the Y-axis. Default is `1`. |
| `transform` | `object` | An ART `Transform` object to apply complex transformations. Overrides `x`, `y`, `rotation`, `scale`, etc. |
| `cursor` | `string` | The CSS cursor style to apply when hovering over the component. |
| `title` | `string` | A title for the component, often displayed as a tooltip. |
| `opacity` | `number` | The opacity of the component, from `0` (fully transparent) to `1` (fully opaque). Default is `1`. |
| `visible` | `boolean` | Controls the visibility of the component. If `false`, the component is hidden. Default is `true`. |
| `onClick` | `function` | Event handler for click events. |
| `onMouseMove` | `function` | Event handler for mouse move events. |
| `onMouseOver` | `function` | Event handler for mouse over events. |
| `onMouseOut` | `function` | Event handler for mouse out events. |
| `onMouseUp` | `function` | Event handler for mouse up events. |
| `onMouseDown` | `function` | Event handler for mouse down events. |

**Usage Example**

```jsx
import { Surface, Shape } from 'react-art';

function CustomShape() {
  return (
    <Surface width={200} height={200}>
      <Shape
        d="M10 10 C20 20, 40 20, 50 10 S90 0, 100 10"
        fill="#ADFF2F"
        stroke="#32CD32"
        strokeWidth={2}
        x={50}
        y={50}
      />
    </Surface>
  );
}
```

This example draws a curved path using cubic Bezier and smooth cubic Bezier commands, filled with a lime green color and a green stroke.

### Text

The `Text` component renders text within your ART graphics. It supports various font styles, alignment options, and can also render text along a custom path.

**Props**

| Name | Type | Description |
|:---|:---|:---|
| `children` | `string` | The text content to display. Can be a single string or an array of strings. |
| `font` | `string` or `object` | The font to use. Can be a CSS font string (e.g., `"12px Arial"`) or an object `{fontSize, fontStyle, fontVariant, fontWeight, fontFamily}`. |
| `alignment` | `string` | Text alignment. Values: `left`, `center`, `right`. |
| `path` | `object` | An ART `Path` object along which the text should flow. |
| `fill` | `string` or `object` | The fill color (e.g., `"#FF0000"`) or a fill object (`LinearGradient`, `RadialGradient`, `Pattern`). |
| `stroke` | `string` | The stroke color. |
| `strokeWidth` | `number` | The width of the stroke. |
| `strokeCap` | `string` | Specifies the shape to be used at the end of open subpaths when they are stroked. Values: `butt`, `round`, `square`. |
| `strokeJoin` | `string` | Specifies the shape to be used at the corners of paths when they are stroked. Values: `miter`, `round`, `bevel`. |
| `strokeDash` | `array` | An array of numbers specifying the lengths of alternating dashes and gaps for the stroke. |
| `x` | `number` | The x-coordinate of the text's top-left corner relative to its parent. Default is `0`. |
| `y` | `number` | The y-coordinate of the text's top-left corner relative to its parent. Default is `0`. |
| `rotation` | `number` | Rotation angle in degrees. Default is `0`. |
| `originX` | `number` | The x-coordinate of the rotation and scale origin. Defaults to the component's center. |
| `originY` | `number` | The y-coordinate of the rotation and scale origin. Defaults to the component's center. |
| `scale` | `number` | Uniform scale factor for both X and Y axes. Default is `1`. |
| `scaleX` | `number` | Scale factor for the X-axis. Default is `1`. |
| `scaleY` | `number` | Scale factor for the Y-axis. Default is `1`. |
| `transform` | `object` | An ART `Transform` object to apply complex transformations. Overrides `x`, `y`, `rotation`, `scale`, etc. |
| `cursor` | `string` | The CSS cursor style to apply when hovering over the component. |
| `title` | `string` | A title for the component, often displayed as a tooltip. |
| `opacity` | `number` | The opacity of the component, from `0` (fully transparent) to `1` (fully opaque). Default is `1`. |
| `visible` | `boolean` | Controls the visibility of the component. If `false`, the component is hidden. Default is `true`. |
| `onClick` | `function` | Event handler for click events. |
| `onMouseMove` | `function` | Event handler for mouse move events. |
| `onMouseOver` | `function` | Event handler for mouse over events. |
| `onMouseOut` | `function` | Event handler for mouse out events. |
| `onMouseUp` | `function` | Event handler for mouse up events. |
| `onMouseDown` | `function` | Event handler for mouse down events. |

**Usage Example**

```jsx
import { Surface, Text } from 'react-art';

function GreetingText() {
  return (
    <Surface width={300} height={100}>
      <Text
        x={50}
        y={30}
        font={{ fontSize: 24, fontWeight: 'bold', fontFamily: 'Arial' }}
        fill="#007bff"
        alignment="center"
      >
        Hello, React ART!
      </Text>
    </Surface>
  );
}
```

This example renders "Hello, React ART!" centered at (50, 30) with a bold, 24px Arial font and a blue fill.

### Additional Drawing Components

React ART also exports several specialized components built on top of the core `Shape` primitive for common geometric forms. While their detailed properties are defined within the ART library itself, you can import and use them directly with `react-art`. For comprehensive documentation on their specific properties, refer to the ART library's official documentation.

*   **Circle**: Renders a circular shape.
*   **Rectangle**: Renders a rectangular shape with optional border radii.
*   **Wedge**: Renders an arc or a partial circular shape.

## Utility Classes

React ART exposes several utility classes from the underlying ART library, primarily for defining complex fills and transformations.

### LinearGradient

The `LinearGradient` class is used to create a linear gradient fill for shapes. You instantiate it with an array of color stops and coordinate pairs defining the gradient direction.

**Constructor**

`new LinearGradient(stops, x1, y1, x2, y2)`

| Parameter | Type | Description |
|:---|:---|:---|
| `stops` | `array` | An array of alternating color strings and offset numbers (e.g., `['red', 0, 'blue', 1]`). |
| `x1`, `y1` | `number` | The starting x and y coordinates of the gradient vector. |
| `x2`, `y2` | `number` | The ending x and y coordinates of the gradient vector. |

**Usage Example**

```jsx
import { Surface, Shape, LinearGradient } from 'react-art';

function GradientShape() {
  const linearGradient = new LinearGradient(
    ['#FF0000', 0, '#0000FF', 1], // Red to Blue
    0, 0, 100, 0 // Horizontal gradient from (0,0) to (100,0)
  );

  return (
    <Surface width={200} height={200}>
      <Shape d="M0 0 L100 0 L100 100 L0 100 Z" fill={linearGradient} x={50} y={50} />
    </Surface>
  );
}
```

This example creates a square filled with a linear gradient transitioning from red to blue horizontally.

### RadialGradient

The `RadialGradient` class is used to create a radial gradient fill. It requires color stops and parameters defining the inner and outer circles of the gradient.

**Constructor**

`new RadialGradient(stops, fx, fy, rx, ry, cx, cy)`

| Parameter | Type | Description |
|:---|:---|:---|
| `stops` | `array` | An array of alternating color strings and offset numbers. |
| `fx`, `fy` | `number` | The x and y coordinates of the focal point (center of the inner circle). |
| `rx`, `ry` | `number` | The x and y radii of the inner circle. |
| `cx`, `cy` | `number` | The x and y coordinates of the center of the outer circle. |

**Usage Example**

```jsx
import { Surface, Shape, RadialGradient } from 'react-art';

function RadialGradientShape() {
  const radialGradient = new RadialGradient(
    ['#FFFF00', 0, '#FFA500', 1], // Yellow to Orange
    50, 50, 0, 0, // Focal point at (50,50), inner radius 0
    50, 50, 50, 50 // Outer circle at (50,50) with radius 50
  );

  return (
    <Surface width={200} height={200}>
      <Shape d="M0 0 L100 0 L100 100 L0 100 Z" fill={radialGradient} x={50} y={50} />
    </Surface>
  );
}
```

This example creates a square filled with a radial gradient transitioning from yellow (center) to orange (edges).

### Pattern

The `Pattern` class allows you to fill a shape with an image pattern. You provide the image URL and the dimensions of the pattern tile.

**Constructor**

`new Pattern(url, width, height, left, top)`

| Parameter | Type | Description |
|:---|:---|:---|
| `url` | `string` | The URL of the image to use as a pattern. |
| `width` | `number` | The width of the pattern tile. |
| `height` | `number` | The height of the pattern tile. |
| `left`, `top` | `number` | The x and y offsets for the top-left corner of the pattern. |

**Usage Example**

```jsx
import { Surface, Shape, Pattern } from 'react-art';

function PatternShape() {
  const imagePattern = new Pattern(
    'https://via.placeholder.com/20x20.png?text=Tile', // Placeholder image URL
    20, 20 // Tile dimensions
  );

  return (
    <Surface width={200} height={200}>
      <Shape d="M0 0 L100 0 L100 100 L0 100 Z" fill={imagePattern} x={50} y={50} />
    </Surface>
  );
}
```

This example creates a square filled with a repeating image pattern from the specified URL.

### Path

The `Path` object (exported as `Path`) is a core ART library class used to define geometric paths, typically used with the `Shape` component's `d` prop or the `Text` component's `path` prop. It provides methods for creating complex shapes using a sequence of commands (e.g., `moveTo`, `lineTo`, `arc`, `curveTo`).

### Transform

The `Transform` object (exported as `Transform`) is a utility class from the ART library that allows for constructing and applying complex affine transformations (translation, rotation, scaling, skewing). While components like `Shape` and `Group` accept individual `x`, `y`, `rotation`, and `scale` props, the `transform` prop allows you to provide a pre-constructed `Transform` instance for more intricate transformations.

## Other Exports

### version

The `version` export provides the version string of the React library that `react-art` is built upon. This can be useful for debugging or ensuring compatibility with your React environment.

**Usage Example**

```jsx
import { version } from 'react-art';

console.log(`React ART is using React version: ${version}`);
```

This section provided a comprehensive overview of the public APIs available in `react-art`, including detailed component properties and utility classes for advanced graphics manipulation. For guidance on common issues and debugging, proceed to the [Troubleshooting and Support](./troubleshooting.md) section.