# client_capabilities.json 说明文档

## 文件概述

本文件定义了 A2UI 客户端能力声明（Client Capabilities）的 JSON Schema。该对象由客户端发送给服务端，作为 A2A 元数据的一部分，用于描述客户端的 UI 渲染能力。

## 数据结构

顶层为对象类型，包含一个版本化的能力声明字段 `v0.9`，该字段为必填项。此外在 `$defs` 中定义了内联目录（Catalog）和函数定义（FunctionDefinition）的结构。

## 字段说明

### 顶层字段

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| v0.9 | object | 是 | A2UI 协议 v0.9 版本的客户端能力结构 |

### v0.9 对象字段

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| supportedCatalogIds | string[] | 是 | 客户端支持的组件和函数目录的 URI 列表 |
| inlineCatalogs | Catalog[] | 否 | 内联目录定义数组，可包含组件和函数定义。仅当服务端在能力声明中声明 `acceptsInlineCatalogs: true` 时才应提供 |

### Catalog（目录定义）

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| catalogId | string | 是 | 目录的唯一标识符 |
| components | object | 否 | 目录支持的 UI 组件定义，键为组件名，值为 JSON Schema |
| functions | FunctionDefinition[] | 否 | 目录支持的函数定义列表 |
| theme | object | 否 | 主题属性定义，键为主题属性名（如 `primaryColor`），值为 JSON Schema |

### FunctionDefinition（函数定义）

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| name | string | 是 | 函数的唯一名称 |
| description | string | 否 | 函数功能和使用方法的人类可读描述 |
| parameters | object (JSON Schema) | 是 | 描述函数预期参数（args）的 JSON Schema |
| returnType | string | 是 | 函数返回值类型，可选值：string、number、boolean、array、object、any、void |

## 示例

```json
{
  "v0.9": {
    "supportedCatalogIds": [
      "https://a2ui.org/specification/v0_9/basic_catalog.json"
    ],
    "inlineCatalogs": [
      {
        "catalogId": "mycompany.com:customCatalog",
        "components": {
          "CustomWidget": {
            "type": "object",
            "properties": {
              "component": { "const": "CustomWidget" },
              "data": { "type": "string" }
            }
          }
        },
        "functions": [
          {
            "name": "customValidate",
            "parameters": {
              "type": "object",
              "properties": { "value": { "type": "string" } }
            },
            "returnType": "boolean"
          }
        ],
        "theme": {
          "accentColor": { "type": "string", "pattern": "^#[0-9a-fA-F]{6}$" }
        }
      }
    ]
  }
}
```
