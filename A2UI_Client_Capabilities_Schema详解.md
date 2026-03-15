# A2UI 客户端能力Schema (v0.8)

> 本文件解读 `/specification/v0_8/json/a2ui_client_capabilities_schema.json`

## 概述

这是 A2UI v0.8 版本的**客户端能力Schema**，用于描述客户端的UI渲染能力。当客户端连接到服务器时，会向服务器声明自己支持哪些组件目录（Catalog）和功能。

---

## Schema 结构

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "title": "A2UI Client Capabilities Schema",
  "description": "A schema for the a2uiClientCapabilities object...",
  "type": "object",
  "properties": {
    "supportedCatalogIds": { ... },
    "inlineCatalogs": { ... }
  },
  "required": ["supportedCatalogIds"]
}
```

---

## 字段定义

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `supportedCatalogIds` | array | ✅ | 客户端支持的组件目录URI列表 |
| `inlineCatalogs` | array | ❌ | 内联目录定义（仅当代理声明支持时） |

---

## 1. supportedCatalogIds - 支持的目录列表

客户端声明自己支持的组件目录URI数组。

### 规则

- 必填字段
- 数组类型，每个元素是字符串（目录URI）
- 标准目录v0.8的URI：`https://a2ui.org/specification/v0_8/standard_catalog_definition.json`

### 示例

```json
{
  "supportedCatalogIds": [
    "https://a2ui.org/specification/v0_8/standard_catalog_definition.json"
  ]
}
```

```json
{
  "supportedCatalogIds": [
    "https://a2ui.org/specification/v0_8/standard_catalog_definition.json",
    "mycompany.com:custom_components",
    "restaurant:booking_catalog"
  ]
}
```

---

## 2. inlineCatalogs - 内联目录

当代理声明支持内联目录时（`acceptsInlineCatalogs: true`），客户端可以提供自定义的内联目录定义。

### 规则

- 可选字段
- 引用 `catalog_description_schema.json` 的结构
- 仅在代理支持内联目录时使用

### 示例

```json
{
  "supportedCatalogIds": [
    "https://a2ui.org/specification/v0_8/standard_catalog_definition.json"
  ],
  "inlineCatalogs": [
    {
      "catalogId": "custom:chart_components",
      "components": {
        "BarChart": {
          "type": "object",
          "properties": {
            "data": { "type": "object" },
            "title": { "type": "string" }
          }
        },
        "PieChart": {
          "type": "object",
          "properties": {
            "data": { "type": "object" },
            "legend": { "type": "boolean" }
          }
        }
      },
      "styles": {}
    }
  ]
}
```

---

## 使用场景

### 场景1：基础客户端

只支持标准目录的客户端：

```json
{
  "supportedCatalogIds": [
    "https://a2ui.org/specification/v0_8/standard_catalog_definition.json"
  ]
}
```

### 场景2：支持自定义目录的客户端

客户端预置了自定义目录：

```json
{
  "supportedCatalogIds": [
    "https://a2ui.org/specification/v0_8/standard_catalog_definition.json",
    "mycompany.com:enterprise_components",
    "healthcare:patient_dashboard"
  ]
}
```

### 场景3：支持内联目录的客户端

代理支持动态内联目录，客户端也接受：

```json
{
  "supportedCatalogIds": [
    "https://a2ui.org/specification/v0_8/standard_catalog_definition.json"
  ],
  "inlineCatalogs": [
    {
      "catalogId": "dynamic:temp_catalog",
      "components": {
        "TemporaryWidget": {
          "type": "object",
          "properties": {
            "config": { "type": "object" }
          }
        }
      },
      "styles": {}
    }
  ]
}
```

---

## 通信流程

```
┌─────────────┐                              ┌─────────────┐
│   Client    │                              │   Server    │
│  (Browser)  │                              │  (Agent)    │
└──────┬──────┘                              └──────┬──────┘
       │                                            │
       │  1. 连接握手                               │
       │  ─────────────────────────────────────>   │
       │                                            │
       │  2. 发送客户端能力声明                       │
       │  a2uiClientCapabilities: {                │
       │    supportedCatalogIds: [...],             │
       │    inlineCatalogs: [...]  (可选)           │
       │  }                                         │
       │ <─────────────────────────────────────   │
       │                                            │
       │  3. 服务器根据能力发送合适的UI               │
       │                                            │
```

### 握手示例

**客户端发送能力声明**：
```json
{
  "supportedCatalogIds": [
    "https://a2ui.org/specification/v0_8/standard_catalog_definition.json",
    "acme.corp:custom_components"
  ],
  "inlineCatalogs": [
    {
      "catalogId": "project:special_widgets",
      "components": {
        "ProjectCard": { "type": "object", "properties": { "title": { "type": "string" } } }
      },
      "styles": {}
    }
  ]
}
```

**服务器响应**：
```json
{
  "beginRendering": {
    "surfaceId": "main",
    "root": "root"
  },
  "surfaceUpdate": {
    "surfaceId": "main",
    "components": [
      {
        "id": "root",
        "component": {
          "Column": {
            "children": ["project_list"]
          }
        }
      },
      {
        "id": "project_list",
        "component": {
          "List": {
            "children": {
              "template": {
                "componentId": "project_card_template",
                "dataBinding": "/projects"
              }
            }
          }
        }
      },
      {
        "id": "project_card_template",
        "component": {
          "Card": {
            "child": "project_content"
          }
        }
      },
      {
        "id": "project_content",
        "component": {
          "Text": {
            "text": { "path": "/title" }
          }
        }
      }
    ]
  }
}
```

---

## 与代理能力的交互

客户端能力需要与代理（服务器）的能力匹配：

### 代理端能力声明（示例）

```json
{
  "capabilities": {
    "a2ui": {
      "acceptsInlineCatalogs": true,
      "supportedCatalogIds": [
        "https://a2ui.org/specification/v0_8/standard_catalog_definition.json"
      ]
    }
  }
}
```

### 匹配规则

| 代理能力 | 客户端要求 |
|---------|-----------|
| `acceptsInlineCatalogs: true` | 客户端可以提供 `inlineCatalogs` |
| `acceptsInlineCatalogs: false` | 客户端不应提供 `inlineCatalogs` |
| `supportedCatalogIds: [...]` | 客户端必须声明支持至少一个共同目录 |

### 能力协商示例

**场景：代理支持内联目录**

```json
// 代理能力
{
  "acceptsInlineCatalogs": true,
  "supportedCatalogIds": [
    "https://a2ui.org/specification/v0_8/standard_catalog_definition.json"
  ]
}
```

```json
// 客户端能力
{
  "supportedCatalogIds": [
    "https://a2ui.org/specification/v0_8/standard_catalog_definition.json"
  ],
  "inlineCatalogs": [
    {
      "catalogId": "client:custom_charts",
      "components": {
        "Chart": { "type": "object" }
      },
      "styles": {}
    }
  ]
}
```

---

## Schema 结构图

```
a2ui_client_capabilities_schema.json
├── supportedCatalogIds (required)  - 支持的目录URI列表
│   └── items: string              - 每个元素是目录URI
│
└── inlineCatalogs (optional)       - 内联目录定义
    └── items: $ref               - 引用 catalog_description_schema.json
```

---

## 完整示例

### 完整的能力声明

```json
{
  "supportedCatalogIds": [
    "https://a2ui.org/specification/v0_8/standard_catalog_definition.json",
    "mycompany.com:enterprise_theme",
    "retail:shopping_components"
  ],
  "inlineCatalogs": [
    {
      "catalogId": "experimental:ai_charts",
      "components": {
        "AIBarChart": {
          "type": "object",
          "properties": {
            "data": {
              "type": "object"
            },
            "animations": {
              "type": "boolean"
            },
            "tooltip": {
              "type": "boolean"
            }
          }
        },
        "AILineChart": {
          "type": "object",
          "properties": {
            "data": {
              "type": "object"
            },
            "showArea": {
              "type": "boolean"
            }
          }
        }
      },
      "styles": {
        "chartTheme": {
          "type": "object",
          "properties": {
            "colors": {
              "type": "array",
              "items": { "type": "string" }
            }
          }
        }
      }
    },
    {
      "catalogId": "temporary:promo_banner",
      "components": {
        "PromoBanner": {
          "type": "object",
          "properties": {
            "message": { "type": "string" },
            "dismissible": { "type": "boolean" }
          }
        }
      },
      "styles": {}
    }
  ]
}
```

---

## 总结

A2UI客户端能力Schema的核心作用：

1. **能力声明** - 客户端告诉服务器自己支持哪些组件目录
2. **能力协商** - 服务器根据客户端能力发送合适的UI
3. **内联目录** - 支持动态内联自定义组件
4. **向后兼容** - 通过URI引用标准目录，确保版本兼容

这种设计使得A2UI可以支持从简单到复杂的各种客户端，实现真正的跨平台UI渲染。
