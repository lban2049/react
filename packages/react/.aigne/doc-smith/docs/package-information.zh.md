# 软件包信息

本节提供了 `react` npm 软件包的基本元数据。这些信息直接来源于其 `package.json` 文件，为开发者在版本控制、许可证和重要链接方面提供了快速参考。

## 基本信息

| 键         | 值                                                              |
| ----------- | ------------------------------------------------------------------ |
| **名称**    | `react`                                                            |
| **版本** | `19.1.0`                                                           |
| **描述** | React 是一个用于构建用户界面的 JavaScript 库。        |
| **许可证** | `MIT`                                                              |
| **主页**| <a href="https://react.dev/" target="_blank">https://react.dev/</a>                                                   |
| **仓库**| <a href="https://github.com/facebook/react.git" target="_blank">https://github.com/facebook/react.git</a>                         |
| **问题跟踪** | <a href="https://github.com/facebook/react/issues" target="_blank">https://github.com/facebook/react/issues</a>                   |
| **主入口点** | `index.js`                                                         |
| **Node 引擎** | `>=0.10.0`                                                         |

## 已发布文件

当 `react` 软件包发布到 npm 时，会包含以下文件和目录，这些文件和目录定义了其可分发的内容。

| 文件 / 目录                  |
| --------------------------------- |
| `LICENSE`                         |
| `README.md`                       |
| `index.js`                        |
| `cjs/`                            |
| `compiler-runtime.js`             |
| `jsx-runtime.js`                  |
| `jsx-runtime.react-server.js`     |
| `jsx-dev-runtime.js`              |
| `jsx-dev-runtime.react-server.js` |
| `react.react-server.js`           |

## 模块导出

`react` 软件包使用 `package.json` 中的 `exports` 字段为其公共 API 定义不同环境下的入口，例如标准模块解析环境和 React Server Components 环境。这确保了系统能根据上下文加载正确的文件。

| 入口点          | 默认环境             | React Server 环境            |
| -------------------- | ------------------------------- | ----------------------------------- |
| `.`                  | `./index.js`                    | `./react.react-server.js`           |
| `./jsx-runtime`      | `./jsx-runtime.js`              | `./jsx-runtime.react-server.js`     |
| `./jsx-dev-runtime`  | `./jsx-dev-runtime.js`          | `./jsx-dev-runtime.react-server.js` |
| `./compiler-runtime` | `./compiler-runtime.js`         | `./compiler-runtime.js`             |
| `./package.json`     | `./package.json`                | 未指定                       |
| `./src/*`            | `./src/*`                       | 未指定                       |
