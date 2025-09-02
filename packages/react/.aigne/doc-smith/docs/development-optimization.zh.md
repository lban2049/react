# 开发与优化

本节旨在指导您提升React应用的性能并简化调试过程，尤其是在开发环境中。它涵盖了特定的工具和关键的构建考量，这些都有助于实现更流畅的开发工作流程和更优化的最终产品。您可以在其子章节中找到更详细的信息：[调试工具](./development-optimization-debugging.md) 和 [编译器运行时](./development-optimization-compiler-runtime.md)。

## 调试工具

高效调试在应用程序开发过程中至关重要。本部分介绍仅在开发环境中使用的工具，旨在帮助您检查组件行为、理解渲染流程并查明可能影响应用程序性能或正确性的问题。像 `useDebugValue` 和 `captureOwnerStack` 等关键工具在开发模式下专门用于协助完成这些任务。

要深入了解这些调试工具以及如何利用它们，请参阅[调试工具](./development-optimization-debugging.md)部分。

## 编译器运行时

通过编译时优化，React的性能可以得到显著提升。本节重点介绍React编译器运行时（React Compiler Runtime），解释其功能以及它如何有助于优化您的应用程序。它涵盖了特定的导出，例如 `useMemoCache`（也称为 `c`），这些对于编译时增强至关重要。

要了解React编译器运行时的全部功能和用法，请访问[编译器运行时](./development-optimization-compiler-runtime.md)部分。

了解这些开发和优化方面将为您提供必要的知识和工具，来构建健壮、高性能的React应用程序。有关每个主题的更多详细信息，请导航至专门的子章节：[调试工具](./development-optimization-debugging.md) 和 [编译器运行时](./development-optimization-compiler-runtime.md)。