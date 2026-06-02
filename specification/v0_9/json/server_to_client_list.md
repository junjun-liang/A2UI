# server_to_client_list.json 说明文档

## 文件概述

本文件定义了 A2UI 服务端到客户端消息列表的 JSON Schema。它表示一个由多条服务端到客户端消息组成的数组，每条消息的格式由 `server_to_client.json` 定义。

## 数据结构

顶层为数组类型，数组中的每个元素引用 `server_to_client.json` 中定义的消息结构。

## 字段说明

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| （数组元素） | ServerToClientMessage[] | 是 | 消息列表，每个元素必须符合 `server_to_client.json` 定义的消息格式 |

## 示例

```json
[
  {
    "version": "v0.9",
    "createSurface": {
      "surfaceId": "surface-1",
      "catalogId": "https://a2ui.org/specification/v0_9/basic_catalog.json"
    }
  },
  {
    "version": "v0.9",
    "updateComponents": {
      "surfaceId": "surface-1",
      "components": []
    }
  }
]
```
