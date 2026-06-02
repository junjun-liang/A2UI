# 1_simple_text.json

## 文件概述

最简单的 A2UI 示例，展示如何使用 minimal 目录渲染一段文本。

## UI 场景

展示一个标题文本 "Hello, Minimal Catalog!"，使用 h1 样式。

## 使用的组件

- **Text** - 显示文本内容，usageHint 为 h1

## 消息序列

1. `surfaceUpdate` - 定义组件树（仅一个 Text 组件）
2. `beginRendering` - 开始渲染，指定 minimal 目录

## 示例片段

```json
{
  "surfaceUpdate": {
    "surfaceId": "1_simple_text",
    "components": [
      {
        "id": "root",
        "component": {
          "Text": {
            "text": { "literalString": "Hello, Minimal Catalog!" },
            "usageHint": "h1"
          }
        }
      }
    ]
  }
}
```
