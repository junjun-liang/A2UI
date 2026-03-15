# A2UI v0.10 客户端到服务器消息Schema详解

> 本文件详解 `/specification/v0_10/json/client_to_server.json`

---

## 概述

`client_to_server.json` 是 A2UI v0.10 中定义**客户端到服务器消息**的Schema。这些消息由客户端发送给服务器，用于报告用户交互、函数响应和错误。

---

## Schema 结构

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "type": "object",
  "minProperties": 2,
  "maxProperties": 2,
  "properties": {
    "version": { "const": "v0.10" },
    "action": { ... },
    "functionResponse": { ... },
    "error": { ... }
  },
  "oneOf": [
    { "required": ["action", "version"] },
    { "required": ["functionResponse", "version"] },
    { "required": ["error", "version"] }
  ]
}
```

**规则**：每个消息必须包含 `version` 和 **恰好一个** 消息类型（action、functionResponse 或 error）

---

## 1. version - 版本标识

```json
{ "version": "v0.10" }
```

| 属性 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `version` | const | ✅ | 固定值 `"v0.10"` |

---

## 2. action - 用户操作消息

当用户与UI组件交互时发送。

### 字段定义

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `name` | string | ✅ | 操作名称（来自组件的 action.event.name） |
| `surfaceId` | string | ✅ | 事件来源的表面ID |
| `sourceComponentId` | string | ✅ | 触发事件的组件ID |
| `timestamp` | string | ✅ | ISO 8601时间戳 |
| `context` | object | ✅ | 已解析的上下文数据 |

### 示例

```json
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
}
```

---

## 3. functionResponse - 函数响应消息

客户端响应服务器调用函数时发送。

### 字段定义

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `functionCallId` | object | ✅ | 函数调用ID |
| `value` | any | ✅ | 函数返回值 |

### functionCallId 结构

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `agentId` | string | ❌ | 发起调用的代理ID |
| `callId` | string | ✅ | 函数调用实例的唯一ID |

### 示例

```json
{
  "version": "v0.10",
  "functionResponse": {
    "functionCallId": {
      "agentId": "agent_1",
      "callId": "call_abc123"
    },
    "value": {
      "latitude": 39.9042,
      "longitude": 116.4074,
      "city": "北京"
    }
  }
}
```

---

## 4. error - 错误消息

客户端报告错误时发送。

### 4.1 Validation Failed Error - 验证失败错误

```json
{
  "version": "v0.10",
  "error": {
    "code": "VALIDATION_FAILED",
    "surfaceId": "form_surface",
    "path": "/components/0/text",
    "message": "Expected string, got integer"
  }
}
```

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `code` | const | ✅ | 固定值 `"VALIDATION_FAILED"` |
| `surfaceId` | string | ✅ | 发生错误的表面ID |
| `path` | string | ✅ | 验证失败的JSON路径 |
| `message` | string | ✅ | 错误描述 |

### 4.2 Generic Error - 通用错误

```json
{
  "version": "v0.10",
  "error": {
    "code": "NETWORK_ERROR",
    "message": "Failed to connect to server",
    "surfaceId": "main_surface"
  }
}
```

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `code` | string | ✅ | 错误代码（非VALIDATION_FAILED） |
| `message` | string | ✅ | 错误描述 |
| `surfaceId` | string | ❌ | 发生错误的表面ID |
| `functionCallId` | object | ❌ | 关联的函数调用ID |

---

## 完整示例

### 示例1：表单提交

```json
{
  "version": "v0.10",
  "action": {
    "name": "submit_contact",
    "surfaceId": "contact_form",
    "sourceComponentId": "submit_button",
    "timestamp": "2026-03-15T10:30:00Z",
    "context": {
      "name": "张三",
      "email": "zhangsan@example.com",
      "message": "我想咨询产品信息"
    }
  }
}
```

### 示例2：函数响应

```json
{
  "version": "v0.10",
  "functionResponse": {
    "functionCallId": {
      "callId": "getLocation_call_001"
    },
    "value": {
      "latitude": 39.9042,
      "longitude": 116.4074,
      "accuracy": 100
    }
  }
}
```

### 示例3：验证失败错误

```json
{
  "version": "v0.10",
  "error": {
    "code": "VALIDATION_FAILED",
    "surfaceId": "registration_form",
    "path": "/components/2/value",
    "message": "Email format is invalid"
  }
}
```

### 示例4：网络错误

```json
{
  "version": "v0.10",
  "error": {
    "code": "NETWORK_ERROR",
    "message": "Connection timeout after 30 seconds",
    "surfaceId": "product_list"
  }
}
```

### 示例5：函数调用错误

```json
{
  "version": "v0.10",
  "error": {
    "code": "FUNCTION_ERROR",
    "message": "Camera access denied by user",
    "functionCallId": {
      "callId": "openCamera_call_xyz"
    }
  }
}
```

---

## Schema 结构图

```
client_to_server.json
├── version (required)              - 固定值 "v0.10"
│
├── action (required, 互斥)
│   ├── name                       - 操作名称
│   ├── surfaceId                  - 表面ID
│   ├── sourceComponentId          - 触发组件ID
│   ├── timestamp                  - 时间戳
│   └── context                    - 上下文数据
│
├── functionResponse (required, 互斥)
│   ├── functionCallId
│   │   ├── agentId (optional)
│   │   └── callId
│   └── value                      - 返回值
│
└── error (required, 互斥)
    ├── code                       - 错误代码
    ├── message                    - 错误消息
    ├── surfaceId (conditional)    - 表面ID
    ├── path (conditional)         - JSON路径
    └── functionCallId (conditional) - 函数调用ID
```

---

## 与其他Schema的关系

| Schema | 关系 |
|--------|------|
| `server_to_client.json` | 服务器发送 callFunction，客户端响应 functionResponse |
| `common_types.json` | 定义 CallId 等类型 |
| `client_data_model.json` | action 消息可附带数据模型 |

---

## 总结

A2UI v0.10 的客户端到服务器消息包含：

1. **action** - 用户交互事件
2. **functionResponse** - 函数调用响应
3. **error** - 错误报告（验证失败/通用错误）

每条消息必须包含 `version: "v0.10"` 和恰好一个消息类型。
