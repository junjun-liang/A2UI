# client_data_model.json 说明文档

## 文件概述

本文件定义了 A2UI 协议中**客户端数据模型**的模式。该对象用于将客户端数据模型附加到 A2A 消息的元数据中，放置在 `a2uiClientDataModel` 字段内。

## 数据结构

顶层为对象，包含协议版本和表面数据模型映射。

## 字段说明

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `version` | const (`"v0.10"`) | 是 | 协议版本号 |
| `surfaces` | object | 是 | 表面 ID 到其当前数据模型的映射 |
| `surfaces.<surfaceId>` | object | — | 对应表面的当前数据模型，为标准 JSON 对象 |

## 示例

```json
{
  "version": "v0.10",
  "surfaces": {
    "contact_form_1": {
      "contact": {
        "firstName": "John",
        "lastName": "Doe",
        "email": "john.doe@example.com"
      }
    },
    "settings_surface": {
      "theme": "dark",
      "language": "zh-CN"
    }
  }
}
```
