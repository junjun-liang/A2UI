# client_to_server_list.json 说明文档

## 文件概述

本文件定义了 A2UI 客户端到服务端消息列表的 JSON Schema。它表示一个由多条客户端到服务端消息组成的数组，每条消息的格式由 `client_to_server.json` 定义。

## 数据结构

顶层为数组类型，数组中的每个元素引用 `client_to_server.json` 中定义的消息结构。

## 字段说明

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| （数组元素） | ClientToServerMessage[] | 是 | 消息列表，每个元素必须符合 `client_to_server.json` 定义的消息格式 |

## 示例

```json
[
  {
    "version": "v0.9",
    "action": {
      "name": "submitLogin",
      "surfaceId": "login-surface",
      "sourceComponentId": "loginBtn",
      "timestamp": "2026-01-16T14:30:00Z",
      "context": { "username": "alice" }
    }
  },
  {
    "version": "v0.9",
    "error": {
      "code": "VALIDATION_FAILED",
      "surfaceId": "login-surface",
      "path": "/components/2/text",
      "message": "密码不能为空"
    }
  }
]
```
