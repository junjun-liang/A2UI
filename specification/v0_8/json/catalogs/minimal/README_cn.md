# 最小化 A2UI Catalog（v0.8）

本文件夹包含一个最小化的 A2UI 组件 catalog（`minimal_catalog.json`），用作测试 v0.8 协议新渲染器实现的测试平台。

## 目的

标准 A2UI catalog 是全面的，具有许多组件、函数和布局原语。从头开始构建支持整个 catalog 的新渲染器可能令人望而生畏。最小化 catalog 将范围缩减为一组核心基础组件：

- **Text**：用于渲染文本字符串。
- **Row**：用于水平弹性布局。
- **Column**：用于垂直弹性布局。
- **Button**：用于基本交互和操作分发。
- **TextField**：用于用户输入。

通过首先针对此最小化 catalog，新的渲染器实现可以在扩展到完整标准 catalog 之前建立坚实的基础——涵盖布局算法、组件嵌套、数据绑定和事件处理。

## 严格子集

v0.8 最小化 catalog 是 A2UI v0.8 标准 catalog（`https://a2ui.org/specification/v0_8/standard_catalog_definition.json`）的**严格子集**。这意味着任何对此最小化 catalog 有效的 A2UI 消息对标准 catalog 也同样有效。这允许开发者使用这些最小化示例来测试可能硬编码使用标准 catalog 的现有 v0.8 渲染器。

## 示例

`examples/` 目录包含布局消息的 JSON 数组（`server_to_client_list` 格式），演示了仅使用此最小化 catalog 中定义的组件的各种 UI 场景。这些示例遵循 A2UI v0.8 协议。
