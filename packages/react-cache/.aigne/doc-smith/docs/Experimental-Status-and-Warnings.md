# Experimental Status and Warnings

This section outlines the experimental nature of `react-cache`, its inherent instability, and critical warnings regarding its usage. For an overview of its purpose, refer to the [Overview](./Overview.md) section.

`react-cache` is designed to work exclusively with experimental, yet-to-be-released React features. It is not intended for general use outside of specific contexts for demonstration and as a reference for more advanced caching implementations.

## Unstable API

The API of `react-cache` is highly unstable. It will undergo significant and frequent changes across versions. You should not expect API stability, and any code relying on its current shape is subject to breakage without notice.

## Do Not Use in Production

It is crucial to understand that `react-cache` is published solely for demonstration and experimental purposes. **Do not use this package in any real or production-critical application.** Using it carries significant risks, including unpredictable behavior, breaking changes, and a lack of support for production environments.

**Use it at your own risk.**

This package serves as a demonstration of potential caching implementations and future React features, rather than a stable library for current application development. For stable caching solutions in production, consider alternative approaches.