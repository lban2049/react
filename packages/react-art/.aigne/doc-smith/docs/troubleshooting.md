# Troubleshooting and Support

This section offers guidance on common issues you might encounter while working with React ART, outlines basic debugging techniques, and points you to resources for getting help and reporting bugs.

## Common Issues and Solutions

When developing with React ART, you may face specific errors or unexpected rendering behavior. Understanding the common pitfalls helps in quickly resolving them.

### Unsupported Component Types

React ART supports a specific set of drawing primitives directly mapped from the underlying ART library. Attempting to use an unsupported type will result in an error:

```
Error: ReactART does not support the type "[YOUR_TYPE]"
```

This error indicates that you are trying to render a component type that is not recognized by React ART's internal instance creation logic. The supported types are `ClippingRectangle`, `Group`, `Shape`, and `Text`. Ensure your component names match these types as defined within React ART.

### Text Children Flattening

When rendering `Text` components, ensure that text content is flattened. If you combine multiple string or number children directly, you might encounter an error:

```
Error: Text children should already be flattened.
```

This means that the `Text` component expects its children to be a single string or number, not multiple distinct text nodes. Combine your text content into a single string before passing it as a child to `Text`.

### Advanced React Features (View Transitions, Gesture Transitions)

React ART functions as a secondary renderer within the React ecosystem. Certain advanced features available in primary renderers like React DOM (e.g., View Transitions, Gesture Transitions) are explicitly not implemented or are no-ops in React ART. Attempting to use them will not have the desired effect or might throw an error:

```javascript
// These functions are no-ops or throw errors in React ART
startViewTransition(); // Returns null
startGestureTransition(); // Throws Error: 'Not implemented.'
getCurrentGestureOffset(); // Throws Error: 'startGestureTransition is not yet supported in react-art.'
```

If you require these specific features, you might need to consider a different rendering solution or implement custom logic outside React ART.

### Incorrect Prop Application

Rendering issues can often stem from incorrect property usage. For instance, transformations are applied via `x`, `y`, `rotation`, `scale`, `scaleX`, `scaleY`, `originX`, `originY`, and a `transform` prop. Visual inconsistencies may occur if these are misapplied.

Properties like `opacity`, `visible`, `fill`, `stroke`, `strokeWidth`, `strokeCap`, `strokeJoin`, and `strokeDash` also control visual aspects. Always refer to the [API Reference](./api-reference.md) for the correct properties and their expected values for each component type (e.g., `Shape`, `Text`).

## Debugging Techniques

Debugging React ART applications involves a combination of standard React debugging practices and inspecting the rendered output in your target environment (Canvas, SVG, or VML).

*   **Browser Developer Tools**: For Canvas, SVG, or VML outputs, use your browser's developer tools to inspect the generated elements. For SVG, you can directly see the SVG DOM structure. For Canvas, you can typically inspect the Canvas element itself and check for rendering context errors.
*   **Console Logging**: Log the `props` passed to your ART components and the `instance` objects created by React ART's renderer (the return value of `createInstance` or `getPublicInstance`). This helps verify that your data is correctly flowing to the ART primitives.
*   **Event Listener Inspection**: If events are not firing, verify that `addEventListeners` and `destroyEventListeners` are being called correctly internally when props like `onClick` or `onMouseMove` are supplied.

## Getting Help and Reporting Bugs

If you encounter a bug or need further assistance, you can leverage the following resources:

*   **GitHub Issues**: For reporting bugs, submitting feature requests, or discussing specific technical challenges, the primary channel is the [React GitHub issues page](https://github.com/facebook/react/issues). When reporting a bug, provide a clear description, steps to reproduce, expected behavior, and actual behavior.
*   **React Documentation**: The main [React documentation homepage](https://react.dev/) is a valuable resource for understanding core React concepts that underpin React ART.

## Next Steps

This section provided essential guidance for troubleshooting and seeking support. For detailed information on all available components and their properties, refer to the [API Reference](./api-reference.md) section.