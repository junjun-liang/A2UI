# a2ui_client_capabilities_schema.json

## 文件概述

A2UI 客户端能力声明的 JSON Schema。该文件定义了客户端在连接服务端时发送的能力信息，用于告知服务端客户端支持哪些组件目录以及是否提供内联目录。服务端根据此信息选择合适的组件目录来构建 UI。

## 数据结构

顶层为 JSON Schema Draft 2020-12 对象，包含客户端支持的目录 ID 列表和可选的内联目录定义。

## 字段说明

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| supportedCatalogIds | array\<string\> | 是 | 客户端支持的组件目录 URI 列表。v0.8 标准目录为 https://a2ui.org/specification/v0_8/standard_catalog_definition.json |
| inlineCatalogs | array | 否 | 内联目录定义数组，每项引用 catalog_description_schema.json。仅在服务端声明 acceptsInlineCatalogs: true 时提供 |

## 示例

```json
{
  "supportedCatalogIds": [
    "https://a2ui.org/specification/v0_8/standard_catalog_definition.json",
    "https://a2ui.org/specification/v0_8/catalogs/minimal/minimal_catalog.json"
  ],
  "inlineCatalogs": []
}
```
