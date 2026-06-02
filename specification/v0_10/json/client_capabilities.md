# client_capabilities.json 说明文档

## 文件概述

本文件定义了 A2UI 协议中**客户端能力声明**的模式。客户端通过此对象向服务端描述其 UI 渲染能力，作为 A2A 元数据的一部分发送。

## 数据结构

顶层为对象，包含按协议版本组织的能力声明，以及内联目录和函数定义的子结构。

## 字段说明

### 顶层字段

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `v0.10` | object | 是 | v0.10 版本的客户端能力结构 |
| `v0.10.supportedCatalogIds` | array\<string\> | 是 | 客户端支持的组件和函数目录的 URI 列表 |
| `v0.10.inlineCatalogs` | array\<Catalog\> | 否 | 内联目录定义数组，仅在服务端声明 `acceptsInlineCatalogs: true` 时提供 |

### Catalog（内联目录）

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `catalogId` | string | 是 | 目录的唯一标识符 |
| `components` | object | 否 | 目录支持的 UI 组件定义，每个值为 JSON Schema |
| `functions` | array\<FunctionDefinition\> | 否 | 目录支持的函数定义列表 |
| `theme` | object | 否 | 主题属性定义，每个键为主题属性名，值为 JSON Schema |

### FunctionDefinition（函数定义）

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `name` | string | 是 | 函数的唯一名称 |
| `description` | string | 否 | 函数功能和用法的可读描述 |
| `parameters` | object (JSON Schema) | 是 | 描述函数预期参数的 JSON Schema |
| `returnType` | enum | 是 | 函数返回类型：`string`/`number`/`boolean`/`array`/`object`/`any`/`void` |

## 示例

```json
{
  "v0.10": {
    "supportedCatalogIds": [
      "https://a2ui.org/specification/v0_10/basic_catalog.json"
    ],
    "inlineCatalogs": [
      {
        "catalogId": "mycompany.com:customCatalog",
        "components": {
          "CustomWidget": {
            "type": "object",
            "properties": {
              "component": { "const": "CustomWidget" },
              "value": { "type": "string" }
            }
          }
        },
        "functions": [
          {
            "name": "doSomething",
            "parameters": {
              "type": "object",
              "properties": { "input": { "type": "string" } }
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
