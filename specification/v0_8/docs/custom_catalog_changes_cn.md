# A2UI v0.8 自定义 Catalog 协商变更摘要

本文档总结了 A2UI 协议在 v0.8 中为支持更灵活和强大的自定义 catalog 协商机制所做的变更。它旨在为在代理或渲染器库中实现这些变更的开发者提供指南。

先前涉及单次 `clientUiCapabilities` 消息的机制已被弃用。新方法允许更动态的、按请求的能力声明，使单个客户端能够支持多个 catalog，并允许代理为每个 UI surface 选择最合适的 catalog。

## 协议的关键变更

1.  **代理能力通告（`supportedCatalogIds`、`acceptsInlineCatalogs`）**：代理在协商中的角色已扩展。它现在可以声明支持的 catalog ID 列表，以及是否能够处理客户端"内联"定义的 catalog。
    - **相关文档**：[`a2ui_extension_specification.md`](./a2ui_extension_specification.md)

2.  **通过 A2A 元数据的客户端能力**：客户端现在在 `a2uiClientCapabilities` 对象中发送其能力。关键的是，这不再是独立的消息，而是包含在发送到代理的**每条** A2A 消息的 `metadata` 字段中。
    - 此对象包含 `supportedCatalogIds`（已知 catalog ID 的数组）和可选的 `inlineCatalogs`（完整 catalog 定义的数组）。
    - **相关文档**：新流程在 [`a2ui_protocol.md`](./a2ui_protocol.md#catalog-negotiation) 的 Catalog 协商部分中有说明。
    - **相关 schema**：[`a2ui_client_capabilities_schema.json`](../json/a2ui_client_capabilities_schema.json)

3.  **按 Surface 的 catalog 选择（`beginRendering`）**：代理现在负责为每个 UI surface 选择使用哪个 catalog。它通过 `beginRendering` 消息中新的可选 `catalogId` 字段来发出选择信号。如果省略此字段，客户端必须默认使用 Standard Catalog。
    - **相关文档**：[`a2ui_protocol.md`](./a2ui_protocol.md#catalog-negotiation)
    - **相关 schema**：变更反映在 [`server_to_client.json`](../json/server_to_client.json) 中。

4.  **Catalog 定义 ID（`catalogId`）**：为便于识别，catalog 定义 schema 本身现在有一个必需的 `catalogId` 字段。
    - **相关 schema**：[`catalog_description_schema.json`](../json/catalog_description_schema.json)

---

## 开发者实现指南

### 代理（服务端）库开发者

您的职责是处理客户端声明的能力并做出渲染选择。

1.  **通告能力**：在代理的能力卡中，在 A2UI 扩展块中添加 `supportedCatalogIds` 数组和 `acceptsInlineCatalogs: true` 参数，以声明您支持哪些 catalog 以及是否可以处理动态 catalog。

2.  **解析客户端能力**：在每条传入的 A2A 消息上，您的库必须解析 `metadata.a2uiClientCapabilities` 对象以确定客户端支持哪些 catalog。您将获得一个 `supportedCatalogIds` 列表，可能还有一个 `inlineCatalogs` 列表。

3.  **选择 Catalog**：在渲染 UI 之前，决定使用哪个 catalog。您的选择必须是客户端在能力对象中通告的 catalog 之一。

4.  **在渲染时指定 Catalog**：在为 surface 发送 `beginRendering` 消息时，将 `catalogId` 字段设置为您所选 catalog 的 ID（如 `"https://my-company.com/inline_catalogs/my-custom-catalog"`）。如果您不设置此字段，则隐式请求使用标准 catalog。

5.  **生成合规的 UI**：确保该 surface 后续 `surfaceUpdate` 消息中生成的所有组件都符合所选 catalog 中定义的属性和类型。

### 渲染器（客户端）库开发者

您的职责是准确声明您的能力，并使用代理选择的 catalog 渲染 surface。

1.  **在每次请求时声明能力**：对于您的应用程序发送的每条 A2A 消息，您的库必须将 `a2uiClientCapabilities` 对象注入到顶层 `metadata` 字段中。

2.  **填充 `supportedCatalogIds`**：在能力对象中，用您的渲染器支持的所有预编译 catalog 的字符串标识符填充此数组。如果您的渲染器支持 v0.8 的标准 catalog，您**应该**包含其 ID：`https://a2ui.org/specification/v0_8/standard_catalog_definition.json`。

3.  **提供 `inlineCatalogs`（可选）**：如果您的渲染器支持在运行时动态生成或定义 catalog，请在 `inlineCatalogs` 数组中包含其完整的、有效的 Catalog Definition Document。

4.  **处理 `beginRendering`**：当您的渲染器收到 `beginRendering` 消息时，它必须检查新的 `catalogId` 字段。

5.  **为 surface 选择 Catalog**：
    - 如果 `catalogId` 存在，使用相应的 catalog 渲染该 surface。您的渲染器必须能够从其预编译列表或刚刚发送的内联定义中查找 catalog。
    - 如果 `catalogId` **不存在**，您**必须**默认为该 surface 使用 v0.8 的 Standard Catalog。

6.  **管理多个 Catalog**：您的渲染器必须架构为能够处理同时使用不同 catalog 渲染的多个 surface。将 `surfaceId` 映射到所选 `catalog` 的字典是一种常见的方法。
