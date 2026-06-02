# client_to_server.json 说明文档

## 文件概述

本文件定义了 A2UI 协议中**客户端到服务端**的事件消息模式（JSON Schema）。它描述了客户端如何向服务端报告用户操作、返回函数调用结果以及报告错误。

## 数据结构

顶层结构为对象，必须恰好包含 2 个属性（`minProperties: 2, maxProperties: 2`），其中 `version` 为必填，另一个为以下三种消息类型之一：

| 消息类型 | 说明 |
|---------|------|
| `action` | 报告用户发起的操作事件 |
| `functionResponse` | 返回函数调用的结果 |
| `error` | 报告客户端错误 |

## 字段说明

### 公共字段

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `version` | const (`"v0.10"`) | 是 | 协议版本号 |

### action（用户操作事件）

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `name` | string | 是 | 操作名称，来自组件的 `action.event.name` 属性 |
| `surfaceId` | string | 是 | 事件来源的表面 ID |
| `sourceComponentId` | string | 是 | 触发事件的组件 ID |
| `timestamp` | string (date-time) | 是 | 事件发生的 ISO 8601 时间戳 |
| `context` | object | 是 | 操作上下文的键值对，已解析所有数据绑定 |

### functionResponse（函数响应）

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `functionCallId` | object (CallId) | 是 | 函数调用的唯一 ID，必须与调用时一致 |
| `value` | string/number/boolean/array/object/null | 是 | 函数调用的返回值 |

### error（错误报告）

支持两种错误类型：

**验证失败错误（VALIDATION_FAILED）：**

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `code` | const (`"VALIDATION_FAILED"`) | 是 | 错误代码 |
| `surfaceId` | string | 是 | 发生错误的表面 ID |
| `path` | string | 是 | 验证失败字段的 JSON Pointer 路径 |
| `message` | string | 是 | 验证失败的简短描述 |

**通用错误：**

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `code` | string | 是 | 错误代码（不能为 `VALIDATION_FAILED`） |
| `message` | string | 是 | 错误的简短描述 |
| `surfaceId` | string | 条件必填 | 发生错误的表面 ID（与 `functionCallId` 二选一） |
| `functionCallId` | object (CallId) | 条件必填 | 关联的函数调用 ID（与 `surfaceId` 二选一） |

> **注意：** 通用错误中 `surfaceId` 和 `functionCallId` 必须且只能提供其中一个。

## 示例

### 用户操作事件

```json
{
  "version": "v0.10",
  "action": {
    "name": "submit",
    "surfaceId": "main",
    "sourceComponentId": "btn_submit",
    "timestamp": "2023-10-27T10:00:00Z",
    "context": {
      "foo": "bar"
    }
  }
}
```

### 函数响应

```json
{
  "version": "v0.10",
  "functionResponse": {
    "functionCallId": { "callId": "call-001" },
    "value": { "result": "success", "count": 42 }
  }
}
```

### 验证失败错误

```json
{
  "version": "v0.10",
  "error": {
    "code": "VALIDATION_FAILED",
    "surfaceId": "main",
    "path": "/components/0/text",
    "message": "Invalid type"
  }
}
```

### 函数调用错误

```json
{
  "version": "v0.10",
  "error": {
    "code": "FUNCTION_FAILED",
    "functionCallId": { "callId": "call-002" },
    "message": "Something went wrong"
  }
}
```
