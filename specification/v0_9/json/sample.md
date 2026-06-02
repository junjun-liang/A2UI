# sample.json 说明文档

## 文件概述

本文件定义了 A2UI 示例（Sample）的 JSON Schema。一个示例用于演示 A2UI 组件和消息的使用方式，包含示例名称、描述和一组有序的 A2UI 消息列表。

## 数据结构

顶层为对象类型，包含示例的元信息和消息列表。

## 字段说明

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| name | string | 是 | 示例的显示名称 |
| description | string | 是 | 示例所展示内容的简短描述 |
| messages | ServerToClientMessage[] | 是 | 客户端需要处理的有序 A2UI 消息列表，格式引用 `server_to_client_list.json` |

## 示例

```json
{
  "name": "简单文本示例",
  "description": "展示如何使用 Text 组件显示一段简单文本",
  "messages": [
    {
      "version": "v0.9",
      "createSurface": {
        "surfaceId": "text-demo",
        "catalogId": "https://a2ui.org/specification/v0_9/basic_catalog.json"
      }
    },
    {
      "version": "v0.9",
      "updateComponents": {
        "surfaceId": "text-demo",
        "components": [
          { "id": "root", "component": "Text", "text": "你好，A2UI！" }
        ]
      }
    }
  ]
}
```
