# A2UI v0.10 客户端到服务器消息列表Schema详解

> 本文件详解 `/specification/v0_10/json/client_to_server_list.json`

---

## 概述

`client_to_server_list.json` 是一个简单的包装Schema，用于定义**客户端到服务器消息列表**的格式。

---

## Schema 结构

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "https://a2ui.org/specification/v0_10/client_to_server_list.json",
  "title": "A2UI Client-to-Server Message List",
  "description": "A list of A2UI Client-to-Server messages.",
  "type": "array",
  "items": {
    "$ref": "client_to_server.json"
  }
}
```

---

## 结构说明

| 属性 | 类型 | 说明 |
|------|------|------|
| `type` | string | 固定值 `"array"` |
| `items` | object | 引用 `client_to_server.json` |

---

## 使用场景

### 1. 用户交互序列

客户端发送多个消息时使用：

```json
[
  {
    "version": "v0.10",
    "action": {
      "name": "select_item",
      "surfaceId": "list",
      "sourceComponentId": "item_1",
      "timestamp": "2026-03-15T10:30:00Z",
      "context": { "itemId": "p1" }
    }
  },
  {
    "version": "v0.10",
    "action": {
      "name": "add_to_cart",
      "surfaceId": "list",
      "sourceComponentId": "add_button",
      "timestamp": "2026-03-15T10:31:00Z",
      "context": { "quantity": 1 }
    }
  }
]
```

### 2. 错误报告序列

```json
[
  {
    "version": "v0.10",
    "error": {
      "code": "RENDER_ERROR",
      "message": "Failed to load image",
      "surfaceId": "main"
    }
  }
]
```

---

## 与 client_to_server.json 的关系

- `client_to_server.json` 定义单个消息的格式
- `client_to_server_list.json` 定义消息数组的格式
- 每个数组项必须是符合 `client_to_server.json` 的有效消息

---

## 完整示例

```json
[
  {
    "version": "v0.10",
    "action": {
      "name": "submit_form",
      "surfaceId": "contact_form",
      "sourceComponentId": "submit_button",
      "timestamp": "2026-03-15T10:30:00Z",
      "context": {
        "name": "张三",
        "email": "zhangsan@example.com"
      }
    }
  },
  {
    "version": "v0.10",
    "functionResponse": {
      "functionCallId": {
        "callId": "call_123"
      },
      "value": {
        "latitude": 39.9042,
        "longitude": 116.4074
      }
    }
  },
  {
    "version": "v0.10",
    "error": {
      "code": "NETWORK_ERROR",
      "message": "Failed to connect to server",
      "surfaceId": "main"
    }
  }
]
```

---

## 总结

这是一个简单的包装Schema，用于批量处理A2UI客户端消息。主要用于：

1. **消息序列** - 用户交互序列
2. **批量响应** - 函数响应批量返回
3. **错误报告** - 多个错误同时报告
4. **消息验证** - 确保消息列表中每条消息都符合规范
