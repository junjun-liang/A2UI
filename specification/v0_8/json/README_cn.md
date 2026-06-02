# A2UI JSON Schema 文件

本目录包含 A2UI 协议的正式 JSON Schema 定义。

## Schema 描述

-   `server_to_client.json`：这是从服务端发送到客户端的消息的核心、与 catalog 无关的 schema。它定义了四种主要消息类型（`beginRendering`、`surfaceUpdate`、`dataModelUpdate`、`deleteSurface`）及其结构。在此 schema 中，`surfaceUpdate` 消息中的 `component` 对象是通用的（`"additionalProperties": true`），允许传递任何组件定义。

-   `client_to_server.json`：此 schema 定义了从客户端发送到服务端的事件消息结构。包括用户发起的操作（`userAction`）、错误报告（`error`）以及关键的 `clientUiCapabilities` 消息，该消息允许客户端通知服务端它支持的组件 catalog。

-   `catalog_description_schema.json`：这是一个元 schema，定义了 A2UI 组件 catalog 的结构。catalog 由 `components` 对象和 `styles` 对象组成，其中每个键是组件/样式名称，值是定义其属性的 JSON schema。这允许创建自定义组件集。

-   `standard_catalog_definition.json`：此文件是 catalog 的具体实现，符合 `catalog_description_schema.json`。它定义了作为 A2UI 基线规范一部分的标准组件集（如 `Text`、`Image`、`Row`、`Card`）和样式。

-   `server_to_client_with_standard_catalog.json`：这是服务端到客户端 schema 的已解析、LLM 友好版本。它通过组合 `server_to_client.json` 和 `standard_catalog_definition.json` 生成。在此版本中，通用的 `component` 对象被替换为严格的 `oneOf` 定义，包含标准 catalog 中的每个组件。这提供了一个完整的、严格类型的 schema，非常适合 LLM 用于生成有效的 A2UI 消息，避免歧义。

-   `catalogs/minimal/minimal_catalog.json`：这是标准 catalog 的严格子集，仅包含一组核心基础组件（`Text`、`Row`、`Column`、`Button`、`TextField`）。它设计用于测试新的渲染器实现，并作为开发的更简单入口。
