# client_to_server_list.json 说明文档

## 文件概述

本文件定义了 A2UI 协议中**客户端到服务端消息列表**的模式。它是一个简单的数组包装器，允许在单个载荷中发送多条客户端到服务端消息。

## 数据结构

顶层结构为数组，每个元素引用 `client_to_server.json` 中定义的消息模式。

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| _(数组元素)_ | client_to_server.json | — | 单条客户端到服务端消息 |

## 示例

```json
[
  {
    "version": "v0.10",
    "action": {
      "name": "submit",
      "surfaceId": "main",
      "sourceComponentId": "btn_submit",
      "timestamp": "2023-10-27T10:00:00Z",
      "context": { "foo": "bar" }
    }
  },
  {
    "version": "v0.10",
    "functionResponse": {
      "functionCallId": { "callId": "call-001" },
      "value": true
    }
  }
]
```
