# catalog_description_schema.json

## 文件概述

A2UI 自定义目录描述的 JSON Schema 元模式（meta-schema）。该文件定义了如何描述一个自定义组件目录，包括目录 ID、组件集合和样式集合。客户端可使用此 Schema 来验证和解析自定义目录定义。

## 数据结构

顶层为 JSON Schema Draft 2020-12 对象，定义了自定义目录的三个必填部分：

- `catalogId` - 目录唯一标识符
- `components` - 组件定义集合
- `styles` - 样式定义集合

## 字段说明

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| catalogId | string | 是 | 目录唯一标识符，建议使用自有域名前缀以避免冲突（如 mycompany.com:somecatalog） |
| components | object | 是 | 组件定义映射，每个键为组件名称，值为该组件属性的 JSON Schema（引用 JSON Schema Draft 2020-12） |
| styles | object | 是 | 样式定义映射，每个键为样式名称，值为该样式的 JSON Schema（引用 JSON Schema Draft 2020-12） |

## 示例

```json
{
  "catalogId": "mycompany.com:custom_catalog",
  "components": {
    "CustomWidget": {
      "type": "object",
      "properties": {
        "title": { "type": "string" },
        "color": { "type": "string" }
      },
      "required": ["title"]
    }
  },
  "styles": {
    "backgroundColor": {
      "type": "string",
      "pattern": "^#[0-9a-fA-F]{6}$"
    }
  }
}
```
