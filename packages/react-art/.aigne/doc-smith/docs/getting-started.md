# Getting Started

This section guides you through setting up React ART, from installation to rendering your first graphic. You will learn how to quickly get started with drawing basic vector graphics in your React applications.

## Installation

To use React ART, you need to have Node.js and a package manager like npm or yarn installed. React ART is designed to work with React, so ensure you have a compatible React version installed as well.

Install `react-art` and its peer dependency `react` using npm or yarn:

```bash
npm install react-art react
# or
yarn add react-art react
```

To verify the installation, you can import `version` from `react-art` in a Node.js environment or your application code and log its value:

```javascript
import { version } from 'react-art';

console.log(`React ART Version: ${version}`);
// Expected output: React ART Version: 19.1.0 (or similar, based on installed version)
```

## Your First React ART Graphic

React ART uses the `Surface` component as the root container for all your vector graphics. Inside a `Surface`, you can place various drawing components like `Circle`, `Rectangle`, `Shape`, `Group`, and `Text`.

Let's create a simple React component that renders a blue circle with a red stroke. First, ensure you have an HTML file with a root element, for example, `<div id="root"></div>`.

Then, create your React component:

```jsx
import React from 'react';
import { createRoot } from 'react-dom/client';
import { Surface, Circle } from 'react-art';

const container = document.getElementById('root');
const root = createRoot(container);

function MyFirstArtApp() {
  return (
    <Surface width={200} height={200}>
      {/* Render a blue circle with a red stroke */}
      <Circle
        x={100} // X-coordinate of the circle's center relative to the Surface
        y={100} // Y-coordinate of the circle's center relative to the Surface
        radius={50} // Radius of the circle
        fill="blue" // Fill color of the circle
        stroke="red" // Stroke color of the circle
        strokeWidth={2} // Width of the stroke
      />
    </Surface>
  );
}

root.render(<MyFirstArtApp />);
```

This example renders a `Surface` with a width and height of 200 pixels. Inside it, a `Circle` component is drawn. The `x` and `y` props specify the center of the circle, `radius` defines its size, and `fill`, `stroke`, and `strokeWidth` control its appearance. The `Surface` component automatically selects the most suitable rendering backend (Canvas, SVG, or VML) for your environment, abstracting away the underlying drawing technology.

## Summary

You have successfully set up React ART and rendered your first basic graphic. This is the foundation for creating more complex and interactive vector graphics.

## Next Steps

To dive deeper into how React ART works and its underlying principles, proceed to the [Core Concepts](./core-concepts.md) section.