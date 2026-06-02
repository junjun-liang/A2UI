# client_to_server.json 说明文档

## 文件概述

本文件定义了 A2UI 客户端到服务端事件消息的 JSON Schema。该消息用于客户端向服务端报告用户操作或客户端错误。每条消息必须且只能包含 `action`（用户动作）或 `error`（错误报告）之一。

## 数据结构

顶层为对象类型，包含 `version` 字段和 `action` 或 `error` 二选一。通过 `oneOf` 约束消息必须为动作消息或错误消息之一。

## 字段说明

### 顶层字段

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| version | string (const: "v0.9") | 是 | 协议版本号 |
| action | object | 条件必填 | 用户动作报告（与 error 二选一） |
| error | object | 条件必填 | 客户端错误报告（与 action 二选一） |

### action 对象（用户动作）

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| name | string | 是 | 动作名称，取自组件的 `action.event.name` 属性 |
| surfaceId | string | 是 | 事件来源的界面 ID |
| sourceComponentId | string | 是 | 触发事件的组件 ID |
| timestamp | string (date-time) | 是 | 事件发生时间的 ISO 8601 时间戳 |
| context | object | 是 | 包含组件 `action.event.context` 中键值对的 JSON 对象，所有数据绑定已解析 |

### error 对象（错误报告）

支持两种错误类型：

#### 验证失败错误（VALIDATION_FAILED）

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| code | string (const: "VALIDATION_FAILED") | 是 | 错误代码 |
| surfaceId | string | 是 | 错误发生的界面 ID |
| path | string | 是 | 验证失败字段的 JSON Pointer 路径（如 `/components/0/text`） |
| message | string | 是 | 验证失败原因的简短描述 |

#### 通用错误

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| code | string (非 "VALIDATION_FAILED") | 是 | 错误代码 |
| surfaceId | string | 是 | 错误发生的界面 ID |
| message | string | 是 | 错误原因的简短描述 |

## 示例

### 用户动作消息

```json
{
  "version": "v0.9",
  "action": {
    "name": "submitLogin",
    "surfaceId": "login-surface",
    "sourceComponentId": "loginBtn",
    "timestamp": "2026-01-16T14:30:00Z",
    "context": {
      "username": "alice",
      "remember": true
    }
  }
}
```

### 验证失败错误消息

```json
{
  "version": "v0.9",
  "error": {
    "code": "VALIDATION_FAILED",
    "surfaceId": "login-surface",
    "path": "/components/3/text",
    "message": "用户名不能为空"
  }
}
```

### 通用错误消息

```json
{
  "version": "v0.9",
  "error": {
    "code": "RENDER_ERROR",
    "surfaceId": "login-surface",
    "message": "无法渲染组件：未知的组件类型"
  }
}
```
