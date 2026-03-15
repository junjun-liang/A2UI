# A2UI v0.10 服务器能力Schema详解

> 本文件详解 `/specification/v0_10/json/server_capabilities.json`

---

## 概述

`server_capabilities.json` 是 A2UI v0.10 中定义**服务器能力**的Schema。服务器（代理）通过此Schema向客户端声明自己支持的A2UI功能。

---

## Schema 结构

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "title": "A2UI Server Capabilities Schema",
  "type": "object",
  "properties": {
    "v0.10": { ... }
  },
  "required": ["v0.10"]
}
```

---

## v0.10 能力结构

```json
{
  "v0.10": {
    "supportedCatalogIds": [...],
    "acceptsInlineCatalogs": true
  }
}
```

### 1. supportedCatalogIds - 支持的目录列表

| 属性 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `supportedCatalogIds` | array | ✅ | 服务器可以生成的组件和函数目录ID列表 |

**说明**：
- 每个元素是目录的唯一标识符（不一定是可解析的URI）
- 标准目录：`https://a2ui.org/specification/v0_10/basic_catalog.json`

### 2. acceptsInlineCatalogs - 接受内联目录

| 属性 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `acceptsInlineCatalogs` | boolean | ❌ | 服务器是否接受客户端的内联目录定义 |

**说明**：
- 如果为 `true`，服务器可以处理客户端发送的 `inlineCatalogs`
- 如果省略，默认为 `false`

---

## 完整示例

### 示例1：基础服务器能力

```json
{
  "v0.10": {
    "supportedCatalogIds": [
      "https://a2ui.org/specification/v0_10/basic_catalog.json"
    ]
  }
}
```

**说明**：服务器仅支持标准基础目录。

---

### 示例2：支持多目录的服务器

```json
{
  "v0.10": {
    "supportedCatalogIds": [
      "https://a2ui.org/specification/v0_10/basic_catalog.json",
      "mycompany.com:enterprise_components",
      "healthcare:patient_dashboard"
    ]
  }
}
```

**说明**：服务器支持标准目录以及企业定制目录。

---

### 示例3：支持内联目录的服务器

```json
{
  "v0.10": {
    "supportedCatalogIds": [
      "https://a2ui.org/specification/v0_10/basic_catalog.json"
    ],
    "acceptsInlineCatalogs": true
  }
}
```

**说明**：服务器支持标准目录，并愿意接受客户端发送的内联自定义目录。

---

### 示例4：完整的服务器能力声明

```json
{
  "v0.10": {
    "supportedCatalogIds": [
      "https://a2ui.org/specification/v0_10/basic_catalog.json",
      "acme.corp:custom_charts",
      "retail:shopping_components",
      "finance:transaction_history"
    ],
    "acceptsInlineCatalogs": true
  }
}
```

---

## 使用场景

### 场景1：A2A Agent Card

在A2A协议的Agent Card中嵌入服务器能力：

```json
{
  "name": "Restaurant Assistant",
  "description": "Helps users find and book restaurants",
  "url": "https://api.example.com/agents/restaurant",
  "capabilities": {
    "a2ui": {
      "v0.10": {
        "supportedCatalogIds": [
          "https://a2ui.org/specification/v0_10/basic_catalog.json"
        ],
        "acceptsInlineCatalogs": true
      }
    }
  }
}
```

### 场景2：MCP 协议

在MCP协议中声明服务器能力：

```json
{
  "server": {
    "name": "restaurant-agent",
    "version": "1.0.0"
  },
  "capabilities": {
    "a2ui": {
      "v0.10": {
        "supportedCatalogIds": [
          "https://a2ui.org/specification/v0_10/basic_catalog.json",
          "restaurant:booking_catalog"
        ]
      }
    }
  }
}
```

---

## 能力协商流程

```
Client                                      Server
  |                                            |
  |--- Client Capabilities (with metadata) ---->|
  |    a2uiClientCapabilities: {              |
  |      v0.10: {                           |
  |        supportedCatalogIds: [...],        |
  |        inlineCatalogs: [...]              |
  |      }                                   |
  |    }                                      |
  |                                            |
  |<-- Server Capabilities (Agent Card) -------|
       capabilities: {                      |
         a2ui: {                           |
           v0.10: {                       |
             supportedCatalogIds: [...],    |
             acceptsInlineCatalogs: true   |
           }                               |
         }                                 |
       }                                    |
  |                                            |
  |  (双方协商确定共同支持的目录)                 |
  |                                            |
```

---

## 与客户端能力的关系

| 客户端能力 | 服务器能力 | 结果 |
|-----------|-----------|------|
| 支持目录A | 支持目录A | ✅ 可用 |
| 支持目录A | 不支持目录A | ❌ 不可用 |
| 发送内联目录 | acceptsInlineCatalogs: true | ✅ 服务器接受 |
| 发送内联目录 | acceptsInlineCatalogs: false | ❌ 服务器拒绝 |

---

## Schema 结构图

```
server_capabilities.json
├── v0.10 (required)
│   ├── supportedCatalogIds (required)   - 支持的目录ID列表
│   │   └── items: string
│   │
│   └── acceptsInlineCatalogs (optional) - 是否接受内联目录
│       └── boolean (default: false)
```

---

## 与其他Schema的关系

| Schema | 关系 |
|--------|------|
| `client_capabilities.json` | 客户端能力声明 |
| `basic_catalog.json` | 标准组件目录 |
| `server_to_client.json` | 服务器发送的消息 |

---

## 总结

A2UI v0.10 的服务器能力Schema提供了：

1. **目录声明** - 声明服务器支持哪些组件目录
2. **内联目录支持** - 可选是否接受客户端的自定义内联目录
3. **协议无关** - 可以嵌入A2A、MCP等多种传输协议

这套机制使得客户端和服务器可以在连接时进行能力协商，确保双方使用兼容的功能集。
