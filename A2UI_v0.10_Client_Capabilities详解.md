# A2UI v0.10 客户端能力Schema详解

> 本文件详解 `/specification/v0_10/json/client_capabilities.json`

---

## 概述

`client_capabilities.json` 是 A2UI v0.10 中定义**客户端能力**的Schema。当客户端连接到服务器时，会向服务器声明自己支持的组件目录、函数和功能。

---

## Schema 结构

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "title": "A2UI Client Capabilities Schema",
  "type": "object",
  "properties": {
    "v0.10": { ... }
  },
  "required": ["v0.10"],
  "$defs": {
    "FunctionDefinition": { ... },
    "Catalog": { ... }
  }
}
```

---

## 1. v0.10 能力结构

```json
{
  "v0.10": {
    "supportedCatalogIds": [...],
    "inlineCatalogs": [...]
  }
}
```

### 1.1 supportedCatalogIds - 支持的目录列表

| 属性 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `supportedCatalogIds` | array | ✅ | 客户端支持的组件和函数目录URI列表 |

**说明**：
- 每个元素是目录的唯一标识符（URI）
- 标准目录：`https://a2ui.org/specification/v0_10/basic_catalog.json`
- 客户端可以支持多个目录

### 1.2 inlineCatalogs - 内联目录

| 属性 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `inlineCatalogs` | array | ❌ | 内联目录定义数组 |

**说明**：
- 仅当服务器声明支持内联目录时使用（`acceptsInlineCatalogs: true`）
- 包含自定义组件和函数定义

---

## 2. FunctionDefinition - 函数定义

定义客户端支持的函数接口。

```json
{
  "name": "getLocation",
  "description": "获取用户当前位置",
  "parameters": {
    "type": "object",
    "properties": {
      "enableHighAccuracy": {
        "type": "boolean"
      }
    }
  },
  "returnType": "object"
}
```

### 字段说明

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `name` | string | ✅ | 函数唯一名称 |
| `description` | string | ❌ | 函数的人类可读描述 |
| `parameters` | object | ✅ | 参数的JSON Schema定义 |
| `returnType` | enum | ✅ | 返回值类型 |

### returnType 可选值

| 值 | 说明 |
|-----|------|
| `string` | 字符串 |
| `number` | 数字 |
| `boolean` | 布尔值 |
| `array` | 数组 |
| `object` | 对象 |
| `any` | 任意类型 |
| `void` | 无返回值 |

---

## 3. Catalog - 组件目录

定义组件和函数的集合。

```json
{
  "catalogId": "custom:my_catalog",
  "components": {
    "MyComponent": { ... }
  },
  "functions": [
    { "name": "myFunction", ... }
  ],
  "theme": {
    "primaryColor": { "type": "string" }
  }
}
```

### 字段说明

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `catalogId` | string | ✅ | 目录唯一标识符 |
| `components` | object | ❌ | UI组件定义 |
| `functions` | array | ❌ | 函数定义数组 |
| `theme` | object | ❌ | 主题属性定义 |

---

## 完整示例

### 示例1：基础客户端能力声明

```json
{
  "v0.10": {
    "supportedCatalogIds": [
      "https://a2ui.org/specification/v0_10/basic_catalog.json"
    ]
  }
}
```

**说明**：客户端仅支持标准基础目录。

---

### 示例2：支持自定义目录的客户端

```json
{
  "v0.10": {
    "supportedCatalogIds": [
      "https://a2ui.org/specification/v0_10/basic_catalog.json",
      "mycompany.com:enterprise_components"
    ]
  }
}
```

**说明**：客户端支持标准目录和企业自定义目录。

---

### 示例3：支持内联目录的客户端

```json
{
  "v0.10": {
    "supportedCatalogIds": [
      "https://a2ui.org/specification/v0_10/basic_catalog.json"
    ],
    "inlineCatalogs": [
      {
        "catalogId": "custom:chart_components",
        "components": {
          "BarChart": {
            "type": "object",
            "properties": {
              "data": { "type": "object" },
              "title": { "type": "string" },
              "color": { "type": "string" }
            },
            "required": ["data"]
          },
          "PieChart": {
            "type": "object",
            "properties": {
              "data": { "type": "object" },
              "legend": { "type": "boolean" }
            },
            "required": ["data"]
          }
        },
        "functions": [
          {
            "name": "getChartData",
            "description": "获取图表数据",
            "parameters": {
              "type": "object",
              "properties": {
                "chartId": { "type": "string" }
              }
            },
            "returnType": "object"
          }
        ],
        "theme": {
          "chartColor": { "type": "string" }
        }
      }
    ]
  }
}
```

**说明**：客户端支持标准目录，并内联定义了自定义图表组件和函数。

---

### 示例4：带自定义函数的客户端

```json
{
  "v0.10": {
    "supportedCatalogIds": [
      "https://a2ui.org/specification/v0_10/basic_catalog.json"
    ],
    "inlineCatalogs": [
      {
        "catalogId": "client:device_functions",
        "components": {},
        "functions": [
          {
            "name": "getLocation",
            "description": "获取用户当前位置",
            "parameters": {
              "type": "object",
              "properties": {
                "enableHighAccuracy": {
                  "type": "boolean",
                  "description": "是否使用高精度模式"
                },
                "timeout": {
                  "type": "number",
                  "description": "超时时间（毫秒）"
                }
              }
            },
            "returnType": "object"
          },
          {
            "name": "getCameraStream",
            "description": "获取摄像头流",
            "parameters": {
              "type": "object",
              "properties": {
                "facingMode": {
                  "type": "string",
                  "enum": ["user", "environment"]
                }
              }
            },
            "returnType": "void"
          },
          {
            "name": "vibrate",
            "description": "触发设备振动",
            "parameters": {
              "type": "object",
              "properties": {
                "duration": {
                  "type": "number",
                  "description": "振动时长（毫秒）"
                }
              }
            },
            "returnType": "void"
          }
        ]
      }
    ]
  }
}
```

**说明**：客户端声明了设备相关的自定义函数，服务器可以调用这些函数。

---

### 示例5：完整的能力声明

```json
{
  "v0.10": {
    "supportedCatalogIds": [
      "https://a2ui.org/specification/v0_10/basic_catalog.json",
      "acme.corp:design_system",
      "healthcare:patient_components"
    ],
    "inlineCatalogs": [
      {
        "catalogId": "experimental:ai_widgets",
        "components": {
          "SentimentAnalysis": {
            "type": "object",
            "properties": {
              "text": { "type": "string" },
              "onResult": { "type": "string" }
            },
            "required": ["text"]
          },
          "LanguageTranslator": {
            "type": "object",
            "properties": {
              "sourceText": { "type": "string" },
              "targetLanguage": { "type": "string" },
              "sourceLanguage": { "type": "string" }
            },
            "required": ["sourceText", "targetLanguage"]
          }
        },
        "functions": [
          {
            "name": "analyzeSentiment",
            "description": "分析文本情感",
            "parameters": {
              "type": "object",
              "properties": {
                "text": { "type": "string" }
              },
              "required": ["text"]
            },
            "returnType": "object"
          },
          {
            "name": "translateText",
            "description": "翻译文本",
            "parameters": {
              "type": "object",
              "properties": {
                "text": { "type": "string" },
                "from": { "type": "string" },
                "to": { "type": "string" }
              },
              "required": ["text", "to"]
            },
            "returnType": "string"
          }
        ],
        "theme": {
          "aiAccentColor": { "type": "string" }
        }
      }
    ]
  }
}
```

---

## 使用流程

### 1. 握手阶段

```
Client                                        Server
  |                                              |
  |--- A2A Connection Request (with metadata) --->|
  |    {                                        |
  |      a2uiClientCapabilities: {              |
  |        v0.10: {                             |
  |          supportedCatalogIds: [...],          |
  |          inlineCatalogs: [...]               |
  |        }                                    |
  |      }                                      |
  |    }                                        |
  |                                              |
  |<-- Agent Card Response ---------------------|
       (server capabilities)                    |
```

### 2. 能力匹配

服务器根据客户端能力决定发送何种UI：
- 只使用客户端支持的组件目录
- 使用内联目录时确保客户端声明支持
- 调用客户端声明的函数

### 3. 运行时交互

```json
// 服务器调用客户端函数
{
  "version": "v0.10",
  "functionCallId": "call_123",
  "wantResponse": true,
  "callFunction": {
    "call": {
      "name": "getLocation",
      "arguments": { "enableHighAccuracy": true }
    },
    "returnType": {
      "type": "object",
      "properties": {
        "latitude": { "type": "number" },
        "longitude": { "type": "number" }
      }
    },
    "callableFrom": "clientOrRemote"
  }
}
```

---

## Schema 结构图

```
client_capabilities.json
├── v0.10 (required)
│   ├── supportedCatalogIds (required)    - 支持的目录URI列表
│   │   └── items: string
│   │
│   └── inlineCatalogs (optional)        - 内联目录定义
│       └── items: Catalog
│           ├── catalogId (required)
│           ├── components (optional)
│           ├── functions (optional)
│           └── theme (optional)
│
└── $defs
    ├── FunctionDefinition
    │   ├── name (required)
    │   ├── description (optional)
    │   ├── parameters (required)        - JSON Schema
    │   └── returnType (required)        - enum
    │
    └── Catalog
        ├── catalogId (required)
        ├── components (optional)
        ├── functions (optional)
        └── theme (optional)
```

---

## 与其他Schema的关系

| Schema | 关系 |
|--------|------|
| `server_capabilities.json` | 服务器端对应定义 |
| `basic_catalog.json` | 标准组件目录 |
| `server_to_client.json` | 使用能力信息确定有效载荷 |

---

## 总结

A2UI v0.10 的客户端能力Schema提供了：

1. **版本化能力声明** - 支持多版本协议
2. **多目录支持** - 声明支持多个组件目录
3. **内联目录** - 动态定义自定义组件和函数
4. **函数接口定义** - 完整的函数签名描述
5. **主题定制** - 支持自定义主题属性

这套机制使得A2UI可以灵活支持从简单到复杂的各种客户端实现。
