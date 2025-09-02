# Text

The `Text` component in React ART is used for rendering textual content within your graphics. It provides control over the text string itself, font styling, alignment, and even placing text along a path. Understanding the `Text` component is essential for creating informative and visually appealing ART applications.

For general information on how drawing components fit into the React ART ecosystem, refer to the [Drawing Components Overview](./drawing-components.md). To learn about styling capabilities applicable to `Text` and other components, see [Styling and Interactivity](./styling-interactivity.md).

## Overview

The `Text` component wraps an ART `Text` node, which is a renderable element responsible for displaying text. It leverages ART's capabilities to draw text directly onto the canvas, SVG, or VML surfaces, depending on the active rendering mode.

### Accessing `Text` Properties

You can access certain dimensional properties of the rendered text directly from a `Text` component instance via a `ref`. This allows you to retrieve the actual `height`, `width`, `x`, and `y` coordinates of the text after it has been rendered by ART.

```javascript
import * as React from 'react';
import { Surface, Group, Text } from 'react-art';

class MyTextComponent extends React.Component {
  textRef = React.createRef();

  componentDidMount() {
    if (this.textRef.current) {
      console.log('Text width:', this.textRef.current.width);
      console.log('Text height:', this.textRef.current.height);
    }
  }

  render() {
    return (
      <Surface width={300} height={100}>
        <Group x={50} y={50}>
          <Text ref={this.textRef} font="18px Arial" fill="#000000">
            Hello, React ART!
          </Text>
        </Group>
      </Surface>
    );
  }
}

export default MyTextComponent;
```

This example demonstrates how to use a `ref` to access the `width` and `height` properties of the rendered `Text` element after it has mounted.

## Properties

The `Text` component accepts several properties to control its appearance and behavior. Many of these properties are common to other renderable ART nodes.

| Name | Type | Description | Default | Applicable to | 
|---|---|---|---|---|
| `children` | `string` or `number` | The actual text content to be displayed. Can be a string or a number. | `''` (empty string) | Text | 
| `font` | `string` or `object` | Defines the font styles. Can be a CSS-like string (e.g., `'12px Arial'`) or an object with properties like `fontSize`, `fontStyle`, `fontVariant`, `fontWeight`, `fontFamily`. | `undefined` | Text | 
| `alignment` | `string` | Specifies the text alignment. Common values include `'left'`, `'center'`, `'right'`. | `undefined` | Text | 
| `path` | `Path` or `string` | A `Path` object or string representing the path along which the text should be rendered. | `undefined` | Text | 
| `x` | `number` | The X-coordinate for the position of the text relative to its parent group or surface. | `0` | Node (inherited) | 
| `y` | `number` | The Y-coordinate for the position of the text relative to its parent group or surface. | `0` | Node (inherited) | 
| `rotation` | `number` | Rotation angle in degrees around the text's origin. | `0` | Node (inherited) | 
| `originX` | `number` | X-coordinate of the rotation and scale origin. | `undefined` | Node (inherited) | 
| `originY` | `number` | Y-coordinate of the rotation and scale origin. | `undefined` | Node (inherited) | 
| `scale` | `number` | Uniform scaling factor. | `1` | Node (inherited) | 
| `scaleX` | `number` | X-axis scaling factor. Overrides `scale` for X. | `1` | Node (inherited) | 
| `scaleY` | `number` | Y-axis scaling factor. Overrides `scale` for Y. | `1` | Node (inherited) | 
| `transform` | `Transform` | An `ART.Transform` object for custom matrix transformations. Applied after `x`, `y`, `rotation`, `scale`. | `undefined` | Node (inherited) | 
| `fill` | `string` or `LinearGradient` or `RadialGradient` or `Pattern` | The fill color or pattern for the text. Can be a CSS color string, or an instance of `LinearGradient`, `RadialGradient`, or `Pattern`. | `undefined` | Renderable Node (inherited) | 
| `stroke` | `string` | The stroke color for the text outline. | `undefined` | Renderable Node (inherited) | 
| `strokeWidth` | `number` | The width of the stroke. | `undefined` | Renderable Node (inherited) | 
| `strokeCap` | `string` | The style of line endings for the stroke (e.g., `'butt'`, `'round'`, `'square'`). | `undefined` | Renderable Node (inherited) | 
| `strokeJoin` | `string` | The style of corner joining for the stroke (e.g., `'miter'`, `'round'`, `'bevel'`). | `undefined` | Renderable Node (inherited) | 
| `strokeDash` | `array` | An array of numbers specifying a dash pattern for the stroke (e.g., `[5, 2]` for 5 units on, 2 units off). | `undefined` | Renderable Node (inherited) | 
| `opacity` | `number` | The opacity level of the text, from 0 to 1. | `1` | Node (inherited) | 
| `visible` | `boolean` | If `false`, the text will be hidden. | `true` | Node (inherited) | 
| `onClick` | `function` | Event handler for click events. | `undefined` | Node (inherited) | 
| `onMouseMove` | `function` | Event handler for mouse move events. | `undefined` | Node (inherited) | 
| `onMouseOver` | `function` | Event handler for mouse over events. | `undefined` | Node (inherited) | 
| `onMouseOut` | `function` | Event handler for mouse out events. | `undefined` | Node (inherited) | 
| `onMouseUp` | `function` | Event handler for mouse up events. | `undefined` | Node (inherited) | 
| `onMouseDown` | `function` | Event handler for mouse down events. | `undefined` | Node (inherited) | 

## Usage Examples

### Basic Text Rendering

To display simple text, provide the string content as `children`:

```javascript
import * as React from 'react';
import { Surface, Group, Text } from 'react-art';

function BasicTextExample() {
  return (
    <Surface width={200} height={100}>
      <Group x={10} y={40}>
        <Text fill="blue">
          Hello, ART!
        </Text>
      </Group>
    </Surface>
  );
}

export default BasicTextExample;
```

This example renders the text "Hello, ART!" in blue.

### Font Styling and Alignment

You can customize the font and alignment of your text using the `font` and `alignment` props:

```javascript
import * as React from 'react';
import { Surface, Group, Text } from 'react-art';

function StyledTextExample() {
  return (
    <Surface width={300} height={200}>
      <Group x={10} y={20}>
        <Text 
          font="bold 24px 'Comic Sans MS'"
          fill="#FF5733"
        >
          Big Bold Text
        </Text>
      </Group>
      <Group x={150} y={80}>
        <Text 
          font={{fontSize: 16, fontFamily: 'Arial', fontWeight: 'normal'}}
          fill="#33FF57"
          alignment="center"
        >
          Centered Text
        </Text>
      </Group>
      <Group x={290} y={140}>
        <Text 
          font="italic 14px Verdana"
          fill="#3357FF"
          alignment="right"
        >
          Right-aligned Italic Text
        </Text>
      </Group>
    </Surface>
  );
}

export default StyledTextExample;
```

This example demonstrates different font styles and alignments using both string and object formats for the `font` property.

### Text on a Path

For more advanced text layouts, you can render text along a custom path. This requires importing `Path` from `react-art` and defining a path object.

```javascript
import * as React from 'react';
import { Surface, Group, Text, Path } from 'react-art';

function PathTextExample() {
  // Define a simple curved path
  const curvedPath = new Path()
    .moveTo(20, 50)
    .curveTo(100, 10, 200, 90, 280, 50);

  return (
    <Surface width={300} height={150}>
      <Group>
        <Text 
          font="16px Georgia"
          fill="purple"
          path={curvedPath}
        >
          Text along a curved path
        </Text>
      </Group>
    </Surface>
  );
}

export default PathTextExample;
```

This example showcases how to make text follow a custom `Path`, which can be useful for logos, decorative elements, or complex data visualizations.

---

This section has provided a detailed look into the `Text` component, covering its properties and various usage scenarios from basic display to advanced path following. You can now effectively incorporate textual information into your React ART graphics. To explore how to apply various fills and strokes, or transform your elements, proceed to the [Styling and Interactivity](./styling-interactivity.md) section.