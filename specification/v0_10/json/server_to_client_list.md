# server_to_client_list.json 说明文档

## 文件概述

本文件定义了 A2UI 协议中**服务端到客户端消息列表**的模式。它是一个简单的数组包装器，允许在单个载荷中发送多条服务端到客户端消息。

## 数据结构

顶层结构为数组，每个元素引用 `server_to_client.json` 中定义的消息模式。

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| _(数组元素)_ | server_to_client.json | — | 单条服务端到客户端消息 |

## 示例

```json
[
  {
    "version": "v0.10",
    "createSurface": {
      "surfaceId": "main",
      "catalogId": "https://a2ui.org/specification/v0_10/basic_catalog.json"
    }
  },
  {
    "version": "v0.10",
    "updateComponents": {
      "surfaceId": "main",
      "components": [
        { "id": "root", "component": "Text", "text": "Hello" }
      ]
    }
  }
]
```
