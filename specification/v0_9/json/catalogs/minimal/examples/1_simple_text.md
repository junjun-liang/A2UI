# 1_simple_text.json 说明文档

## 文件概述

本文件是 A2UI 最小化目录的最基础示例，展示了如何使用 Text 组件渲染一段简单文本。

## UI 场景

展示一个简单的标题文本界面，使用 h1 变体样式显示 "Hello, Minimal Catalog!"。

## 使用的组件

| 组件 | 用途 |
|------|------|
| Text | 显示标题文本，variant 为 h1 |

## 消息流程

1. **createSurface** — 创建界面 `example_1`，使用 minimal_catalog 目录
2. **updateComponents** — 设置根组件为 Text，显示 "Hello, Minimal Catalog!"，使用 h1 样式

## 示例关键片段

```json
{
  "id": "root",
  "component": "Text",
  "text": "Hello, Minimal Catalog!",
  "variant": "h1"
}
```
