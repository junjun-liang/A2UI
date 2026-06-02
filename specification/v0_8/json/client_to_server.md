# client_to_server.json

## 文件概述

A2UI 客户端到服务端事件消息的 JSON Schema 定义。该文件描述了客户端如何向服务端报告用户操作和客户端错误。每条消息必须恰好包含 `userAction` 或 `error` 中的一个。

## 数据结构

顶层为 JSON Schema 对象，使用 `oneOf` 约束确保消息只能包含以下两种类型之一：

- `userAction` - 用户操作事件
- `error` - 客户端错误报告

## 字段说明

### 顶层字段

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| userAction | object | 条件必填 | 报告用户发起的操作事件 |
| error | object | 条件必填 | 报告客户端错误，内容灵活（additionalProperties: true） |

> 注意：`userAction` 和 `error` 二选一，由 `oneOf` 约束。

### userAction 字段

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| name | string | 是 | 操作名称，取自组件的 action.name 属性 |
| surfaceId | string | 是 | 事件来源的表面 ID |
| sourceComponentId | string | 是 | 触发事件的组件 ID |
| timestamp | string (date-time) | 是 | 事件发生时间的 ISO 8601 时间戳 |
| context | object | 是 | 包含组件 action.context 解析后的键值对（additionalProperties: true） |

### error 字段

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| (任意属性) | any | - | 错误内容灵活，无固定结构 |

## 示例

### 用户操作事件

```json
{
  "userAction": {
    "name": "login_submitted",
    "surfaceId": "login-screen",
    "sourceComponentId": "submit_button",
    "timestamp": "2024-12-15T10:30:00Z",
    "context": {
      "user": "alice",
      "pass": "secret123"
    }
  }
}
```

### 错误报告

```json
{
  "error": {
    "type": "rendering_error",
    "message": "Failed to render component: Image",
    "surfaceId": "main-screen"
  }
}
```
