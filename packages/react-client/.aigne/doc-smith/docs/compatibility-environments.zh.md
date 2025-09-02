# 兼容性与环境
`react-client` 包旨在适应各种 JavaScript 运行时环境。其核心功能依赖于特定环境的配置，这些配置通常通过填充 `ReactFlightClientConfig.js` 模块并使用针对特定宿主环境定制的实现来提供。

### 配置模块化
这种模块化方法允许核心 `ReactFlightClient.js` 逻辑保持通用，而特定行为则通过专门的配置文件注入。这些配置决定了 `react-client` 如何与其周围环境交互，包括模块加载、数据流和控制台输出的详细信息。

主要可配置方面包括：

*   **`rendererVersion`**：正在使用的 React 渲染器的版本标识符。
*   **`rendererPackageName`**：与渲染器关联的包名称（例如，`react-server-dom-webpack`）。
*   **Stream Configuration**：确定适当的流处理机制，无论是针对 Node.js 环境（`ReactFlightClientStreamConfigNode`）优化的流还是 Web 兼容流（`ReactFlightClientStreamConfigWeb`）。
*   **Console Configuration**：管理源自服务器的控制台消息如何在客户端处理和显示，利用 `ReactClientConsoleConfigServer`、`ReactClientConsoleConfigBrowser` 或 `ReactClientConsoleConfigPlain` 等配置。
*   **Bundler Integration**：定义客户端引用和模块如何解析、预加载和按需加载，其中包含针对 Webpack、Turbopack、Parcel 和 ESM 等打包器的具体实现。
*   **`usedWithSSR`**：一个布尔标志，指示客户端是否在服务器端渲染 (SSR) 上下文下运行。

### 环境配置文件
`react-client` 通过不同的配置配置文件支持各种运行时环境。下表总结了这些配置文件的主要特性：

| Configuration Profile | Runtime Environment | Bundler Integration | `usedWithSSR` | Console Configuration | Stream Configuration | Special Notes |
|---|---|---|---|---|---|---|
| `dom-node-turbopack` | Node.js | Turbopack | `true` | Server | Node | |
| `dom-node-webpack` | Node.js | Webpack | `true` | Server | Node | 包括 `dom-node.js` 和 `dom-node-webpack.js` |
| `dom-node-parcel` | Node.js | Parcel | `true` | Server | Node | |
| `dom-node-esm` | Node.js | ESM | `true` | Server | Node | |
| `dom-edge-webpack` | Edge | Webpack | `true` | Server | Web | |
| `dom-edge-turbopack` | Edge | Turbopack | `true` | Server | Web | |
| `dom-edge-parcel` | Edge | Parcel | `true` | Server | Web | |
| `dom-browser-turbopack` | Browser | Turbopack | `false` | Browser | Web | |
| `dom-browser-webpack` | Browser | Webpack | `false` | Browser | Web | 包括 `dom-browser.js` |
| `dom-browser-esm` | Browser | ESM | `false` | Browser | Web | |
| `dom-browser-parcel` | Browser | Parcel | `false` | Browser | Web | |
| `markup` | Generic (HTML Markup) | None | `true` | Plain | Markup (Legacy) | 如果发出客户端引用，则抛出错误。 |
| `dom-legacy` | Generic (DOM) | Mixed | `true` | Browser | Web | `rendererPackageName` 为 'not-used'。引用解析为 null。 |
| `dom-bun` | Bun | Mixed | `true` | Plain | Web | 引用解析为 null。 |
| `custom` | Custom | Custom | `true` | Custom | Custom | 由第三方渲染器使用；所有配置都通过 `$$$config` 全局变量外部提供。 |

### SSR 对配置的影响
`usedWithSSR` 标志显著影响 `react-client` 的预期行为和功能。当 `usedWithSSR` 设置为 `true` 时，配置预期与服务器端渲染管道集成。这通常需要服务器特定的控制台行为、处理服务器引用的机制以及优化的预初始化提示，以实现更快的内容交付。

相反，当 `usedWithSSR` 为 `false` 时，配置通常面向纯客户端环境。在这种情况下，某些与服务器相关的功能可能会被省略或行为不同，因为除了初始注水外，客户端不期望直接与活动的服务器端渲染过程交互。

### 结论
通过利用这些特定于环境的配置，`react-client` 确保在各种部署场景中进行健壮和优化的操作，从处理 SSR 的 Node.js 服务器到纯客户端浏览器应用程序。这种适应性对于无论底层 JavaScript 运行时或打包器如何，都能保持一致的行为和性能至关重要。要了解有关这些配置如何促进通信的更多信息，请参阅“[服务器-客户端交互](./core-concepts-server-client-interaction.md)”部分。
