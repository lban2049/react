# Fills and Strokes

In React ART, you can apply a variety of visual styles to your drawing components using `fill` and `stroke` properties. These properties control the interior color or pattern of a shape and the appearance of its border, respectively. Understanding how to utilize solid colors, gradients, and patterns, along with various stroke customizations, allows for the creation of rich and dynamic graphics.

For an overview of the core drawing components, refer to the [Drawing Components](./drawing-components.md) section. To learn about positioning and resizing your elements, see [Transformations](./styling-interactivity-transformations.md).

## Applying Solid Fills and Strokes

The most basic way to style your ART elements is by applying solid colors to their `fill` and `stroke` properties. You can specify colors using standard CSS color names (e.g., `"red"`) or hexadecimal color codes (e.g., `"#FF0000"`).

### `fill` Property

The `fill` property determines the color or pattern used to paint the interior of a shape.

**Parameters**

| Name | Type | Description |
|---|---|---| 
| `fill` | `string` or `object` | Specifies the fill color (as a string) or a gradient/pattern object (e.g., `LinearGradient`, `RadialGradient`, `Pattern`). |

**Example: Solid Color Fill**

```javascript
import * as React from 'react';
import { Surface, Shape } from 'react-art';

function SolidFillExample() {
  return (
    <Surface width={200} height={200}>
      <Shape
        d="M10 10 L190 10 L100 190 Z" // A simple triangle path
        fill="#4CAF50" // Green fill
      />
    </Surface>
  );
}

export default SolidFillExample;
```

This example renders a green triangle using a hexadecimal color code for the fill.

### `stroke` Property

The `stroke` property defines the color of the border (outline) of a shape.

**Parameters**

| Name | Type | Description |
|---|---|---| 
| `stroke` | `string` | Specifies the stroke color as a string. |

**Example: Solid Color Stroke**

```javascript
import * as React from 'react';
import { Surface, Shape } from 'react-art';

function SolidStrokeExample() {
  return (
    <Surface width={200} height={200}>
      <Shape
        d="M10 10 L190 10 L100 190 Z"
        fill="none" // No fill
        stroke="#2196F3" // Blue stroke
        strokeWidth={3}
      />
    </Surface>
  );
}

export default SolidStrokeExample;
```

This example draws an outline of a triangle with a blue stroke and no fill.

## Gradients for Fills

React ART supports linear and radial gradients, allowing you to create smooth color transitions within your shapes. These are instantiated as `LinearGradient` and `RadialGradient` objects and passed to the `fill` prop.

### `LinearGradient`

A `LinearGradient` transitions colors along a straight line. You define its colors, their positions (stops), and the start/end points of the gradient.

**Constructor**

`new LinearGradient(stops, x1, y1, x2, y2)`

**Parameters**

| Name | Type | Description |
|---|---|---| 
| `stops` | `Array<string \| Array<string \| number>>` | An array of color stops. Each stop can be a color string (e.g., `"red"`) or an array `[color, offset]` where `offset` (0-1) specifies the color's position along the gradient. |
| `x1`, `y1` | `number` | The starting x and y coordinates of the gradient line. |
| `x2`, `y2` | `number` | The ending x and y coordinates of the gradient line. |

**Example: Linear Gradient Fill**

```javascript
import * as React from 'react';
import { Surface, Shape, LinearGradient } from 'react-art';

function LinearGradientExample() {
  const gradient = new LinearGradient(
    [['#FF0000', 0], ['#FFFF00', 0.5], ['#0000FF', 1]], // Red to Yellow to Blue
    0, 0, // Start X, Y
    200, 200 // End X, Y
  );

  return (
    <Surface width={200} height={200}>
      <Shape
        d="M0 0 H200 V200 H0 Z" // A rectangle path
        fill={gradient}
      />
    </Surface>
  );
}

export default LinearGradientExample;
```

This example fills a rectangle with a linear gradient transitioning from red to yellow to blue.

### `RadialGradient`

A `RadialGradient` transitions colors circularly or elliptically, radiating from a central point.

**Constructor**

`new RadialGradient(stops, fx, fy, rx, ry, cx, cy)`

**Parameters**

| Name | Type | Description |
|---|---|---| 
| `stops` | `Array<string \| Array<string \| number>>` | An array of color stops, same as `LinearGradient`. |
| `fx`, `fy` | `number` | The focal point (inner circle's center) of the gradient. |
| `rx`, `ry` | `number` | The x and y radii of the gradient's ending shape. |
| `cx`, `cy` | `number` | The center point of the outer circle/ellipse. |

**Example: Radial Gradient Fill**

```javascript
import * as React from 'react';
import { Surface, Shape, RadialGradient } from 'react-art';

function RadialGradientExample() {
  const gradient = new RadialGradient(
    [['#FFFFFF', 0], ['#00FF00', 0.5], ['#000000', 1]], // White to Green to Black
    100, 100, // Focal X, Y
    100, 100, // Radius X, Y
    100, 100 // Center X, Y
  );

  return (
    <Surface width={200} height={200}>
      <Shape
        d="M0 0 H200 V200 H0 Z" // A rectangle path
        fill={gradient}
      />
    </Surface>
  );
}

export default RadialGradientExample;
```

This example creates a radial gradient fill, radiating from the center of the rectangle.

## Patterns for Fills

A `Pattern` allows you to fill a shape with a repeating image. This is useful for textures or repeating background elements.

**Constructor**

`new Pattern(url, width, height, left, top)`

**Parameters**

| Name | Type | Description |
|---|---|---| 
| `url` | `string` | The URL of the image to use as a pattern. |
| `width` | `number` | The width of the pattern tile. |
| `height` | `number` | The height of the pattern tile. |
| `left` | `number` | The horizontal offset of the pattern. |
| `top` | `number` | The vertical offset of the pattern. |

**Example: Pattern Fill**

```javascript
import * as React from 'react';
import { Surface, Shape, Pattern } from 'react-art';

// Note: Replace with a valid image URL that is publicly accessible.
const imageUrl = 'https://react.dev/favicon.ico'; // Example image

function PatternFillExample() {
  const pattern = new Pattern(imageUrl, 32, 32, 0, 0);

  return (
    <Surface width={200} height={200}>
      <Shape
        d="M0 0 H200 V200 H0 Z" // A rectangle path
        fill={pattern}
      />
    </Surface>
  );
}

export default PatternFillExample;
```

This example fills a rectangle with a repeating pattern using a specified image.

## Stroke Customization

Beyond just color, you can control various aspects of a shape's stroke, including its width, the style of its ends (caps), and how corners are rendered (joins).

### Stroke Properties

| Name | Type | Description |
|---|---|---| 
| `strokeWidth` | `number` | The thickness of the stroke. Default is 1. |
| `strokeCap` | `string` | Defines the shape used for the ends of open paths. Possible values: `"butt"` (flat end, extends to the exact end of the path), `"round"` (rounded end), `"square"` (flat end, extends beyond the path by half the stroke width). |
| `strokeJoin` | `string` | Defines the shape used to join two line segments. Possible values: `"miter"` (sharp corner), `"round"` (rounded corner), `"bevel"` (flat corner). |
| `strokeDash` | `Array<number>` | An array of numbers specifying the lengths of alternating dashes and gaps. For example, `[5, 5]` creates a dashed line with 5-unit dashes and 5-unit gaps. |

**Example: Customizing Strokes**

```javascript
import * as React from 'react';
import { Surface, Shape } from 'react-art';

function StrokeCustomizationExample() {
  return (
    <Surface width={300} height={300}>
      {/* Thick stroke with round caps and joins */}
      <Shape
        d="M20 20 L100 20 L100 80"
        stroke="#E91E63"
        strokeWidth={10}
        strokeCap="round"
        strokeJoin="round"
      />

      {/* Dashed stroke */}
      <Shape
        d="M20 120 L180 120"
        stroke="#9C27B0"
        strokeWidth={2}
        strokeDash={[5, 5]} // 5 units dash, 5 units gap
      />

      {/* Different stroke joins */}
      <Shape
        d="M20 200 L100 200 L100 260"
        stroke="#FFC107"
        strokeWidth={8}
        strokeJoin="miter"
      />
      <Shape
        d="M120 200 L200 200 L200 260"
        stroke="#FFC107"
        strokeWidth={8}
        strokeJoin="round"
      />
      <Shape
        d="M220 200 L300 200 L300 260"
        stroke="#FFC107"
        strokeWidth={8}
        strokeJoin="bevel"
      />
    </Surface>
  );
}

export default StrokeCustomizationExample;
```

This example demonstrates various stroke properties: a thick stroke with rounded ends and corners, a dashed line, and different `strokeJoin` styles (miter, round, bevel) applied to similar paths for comparison.

---

This section covered how to use solid colors, linear gradients, radial gradients, and patterns to fill shapes, as well as how to customize strokes with properties like width, cap, join, and dash. These styling options provide granular control over the visual presentation of your React ART graphics. Next, explore how to apply dynamic positioning and sizing to your elements in the [Transformations](./styling-interactivity-transformations.md) section.
