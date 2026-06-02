# client_data_model.json 说明文档

## 文件概述

本文件定义了 A2UI 客户端数据模型（Client Data Model）的 JSON Schema。该对象用于将客户端的数据模型附加到 A2A 消息的元数据中，应放置在元数据的 `a2uiClientDataModel` 字段内。

## 数据结构

顶层为对象类型，包含协议版本号和界面（surface）数据模型映射。

## 字段说明

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| version | string (const: "v0.9") | 是 | 协议版本号，固定为 `v0.9` |
| surfaces | object | 是 | 界面 ID 到其当前数据模型的映射表。键为 surfaceId，值为该界面的当前数据模型（标准 JSON 对象） |

## 示例

```json
{
  "version": "v0.9",
  "surfaces": {
    "surface-1": {
      "username": "alice",
      "isLoggedIn": true
    },
    "surface-2": {
      "items": ["apple", "banana"]
    }
  }
}
```
