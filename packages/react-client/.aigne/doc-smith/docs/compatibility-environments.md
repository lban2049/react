# Compatibility & Environments
The `react-client` package is designed for adaptability across various JavaScript runtime environments. Its core functionality relies on environment-specific configurations, which are typically provided by shimming the `ReactFlightClientConfig.js` module with an implementation tailored to the specific host environment.

### Configuration Modularity
This modular approach allows the core `ReactFlightClient.js` logic to remain generic while specific behaviors are injected through specialized configuration files. These configurations dictate how `react-client` interacts with its surroundings, including details on module loading, data streaming, and console output.

Key configurable aspects include:

*   **`rendererVersion`**: The version identifier for the React renderer being used.
*   **`rendererPackageName`**: The package name associated with the renderer (e.g., `react-server-dom-webpack`).
*   **Stream Configuration**: Determines the appropriate stream handling mechanism, whether it's optimized for Node.js environments (`ReactFlightClientStreamConfigNode`) or web-compatible streams (`ReactFlightClientStreamConfigWeb`).
*   **Console Configuration**: Manages how console messages originating from the server are processed and displayed in the client, utilizing configurations like `ReactClientConsoleConfigServer`, `ReactClientConsoleConfigBrowser`, or `ReactClientConsoleConfigPlain`.
*   **Bundler Integration**: Defines how client references and modules are resolved, preloaded, and required, with specific implementations for bundlers such as Webpack, Turbopack, Parcel, and ESM.
*   **`usedWithSSR`**: A boolean flag indicating whether the client is operating in a Server-Side Rendering (SSR) context.

### Environment Profiles
`react-client` supports a variety of runtime environments through distinct configuration profiles. The table below summarizes the key characteristics of these profiles:

| Configuration Profile | Runtime Environment | Bundler Integration | `usedWithSSR` | Console Configuration | Stream Configuration | Special Notes |
|---|---|---|---|---|---|---|
| `dom-node-turbopack` | Node.js | Turbopack | `true` | Server | Node | |
| `dom-node-webpack` | Node.js | Webpack | `true` | Server | Node | Includes `dom-node.js` and `dom-node-webpack.js` |
| `dom-node-parcel` | Node.js | Parcel | `true` | Server | Node | |
| `dom-node-esm` | Node.js | ESM | `true` | Server | Node | |
| `dom-edge-webpack` | Edge | Webpack | `true` | Server | Web | |
| `dom-edge-turbopack` | Edge | Turbopack | `true` | Server | Web | |
| `dom-edge-parcel` | Edge | Parcel | `true` | Server | Web | |
| `dom-browser-turbopack` | Browser | Turbopack | `false` | Browser | Web | |
| `dom-browser-webpack` | Browser | Webpack | `false` | Browser | Web | Includes `dom-browser.js` |
| `dom-browser-esm` | Browser | ESM | `false` | Browser | Web | |
| `dom-browser-parcel` | Browser | Parcel | `false` | Browser | Web | |
| `markup` | Generic (HTML Markup) | None | `true` | Plain | Markup (Legacy) | Throws errors if client references are emitted. |
| `dom-legacy` | Generic (DOM) | Mixed | `true` | Browser | Web | `rendererPackageName` is 'not-used'. Reference resolution is null. |
| `dom-bun` | Bun | Mixed | `true` | Plain | Web | Reference resolution is null. |
| `custom` | Custom | Custom | `true` | Custom | Custom | Used by third-party renderers; all configurations are externally provided via a `$$$config` global. |

### Impact of SSR on Configuration
The `usedWithSSR` flag significantly influences the expected behavior and features of `react-client`. When `usedWithSSR` is set to `true`, the configuration anticipates integration with a server-side rendering pipeline. This often entails server-specific console behaviors, mechanisms for handling server references, and optimized preinitialization hints for faster content delivery.

Conversely, when `usedWithSSR` is `false`, the configuration is typically geared towards purely client-side environments. In such cases, certain server-related features might be omitted or behave differently, as the client is not expected to interact directly with an active server-side rendering process beyond initial hydration.

### Conclusion
By leveraging these environment-specific configurations, `react-client` ensures robust and optimized operation across diverse deployment scenarios, from Node.js servers handling SSR to pure client-side browser applications. This adaptability is critical for maintaining consistent behavior and performance regardless of the underlying JavaScript runtime or bundler. To understand more about how these configurations facilitate communication, refer to the [Server-Client Interaction](./core-concepts-server-client-interaction.md) section.