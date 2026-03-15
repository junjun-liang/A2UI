# A2UI v0.10 服务器到客户端消息列表Schema详解

> 本文件详解 `/specification/v0_10/json/server_to_client_list.json`

---

## 概述

`server_to_client_list.json` 是一个简单的包装Schema，用于定义**服务器到客户端消息列表**的格式。

---

## Schema 结构

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "https://a2ui.org/specification/v0_10/server_to_client_list.json",
  "title": "A2UI Server-to-Client Message List",
  "description": "A list of A2UI Server-to-Client messages.",
  "type": "array",
  "items": {
    "$ref": "server_to_client.json"
  }
}
```

---

## 结构说明

| 属性 | 类型 | 说明 |
|------|------|------|
| `type` | string | 固定值 `"array"` |
| `items` | object | 引用 `server_to_client.json` |

---

## 使用场景

### 1. JSONL 流式传输

服务器发送一系列A2UI消息时使用：

```jsonl
{"version": "v0.10", "createSurface": {"surfaceId": "main", "catalogId": "..."}}
{"version": "v0.10", "updateComponents": {"surfaceId": "main", "components": [...]}}
{"version": "v0.10", "updateDataModel": {"surfaceId": "main", "path": "/", "value": {...}}}
```

### 2. 批量消息

```json
[
  {
    "version": "v0.10",
    "createSurface": {
      "surfaceId": "form",
      "catalogId": "https://a2ui.org/specification/v0_10/basic_catalog.json"
    }
  },
  {
    "version": "v0.10",
    "updateComponents": {
      "surfaceId": "form",
      "components": [...]
    }
  }
]
```

---

## 与 server_to_client.json 的关系

- `server_to_client.json` 定义单个消息的格式
- `server_to_client_list.json` 定义消息数组的格式
- 每个数组项必须是符合 `server_to_client.json` 的有效消息

---

## 完整示例

```json
[
  {
    "version": "v0.10",
    "createSurface": {
      "surfaceId": "restaurant_booking",
      "catalogId": "https://a2ui.org/specification/v0_10/basic_catalog.json",
      "theme": {
        "primaryColor": "#FF5722"
      },
      "sendDataModel": true
    }
  },
  {
    "version": "v0.10",
    "updateComponents": {
      "surfaceId": "restaurant_booking",
      "components": [
        {
          "id": "root",
          "component": "Column",
          "children": ["title", "form"]
        },
        {
          "id": "title",
          "component": "Text",
          "text": { "literalString": "餐厅预订" },
          "variant": "h1"
        },
        {
          "id": "form",
          "component": "Column",
          "children": ["name_field", "date_field"]
        }
      ]
    }
  },
  {
    "version": "v0.10",
    "updateDataModel": {
      "surfaceId": "restaurant_booking",
      "path": "/",
      "value": {
        "name": "",
        "date": ""
      }
    }
  }
]
```

---

## 总结

这是一个简单的包装Schema，用于批量处理A2UI消息。主要用于：

1. **流式传输** - JSONL格式
2. **批量消息** - 数组格式
3. **消息验证** - 确保消息列表中每条消息都符合规范
