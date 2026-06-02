# 2_row_layout.json 说明文档

## 文件概述

本文件展示了如何使用 Row 组件实现水平布局，将两个文本组件并排放置。

## UI 场景

展示两个文本组件水平排列的布局：左侧为 "Left Content"（body 样式），右侧为 "Right Content"（caption 样式），使用 spaceBetween 对齐方式使两者分居两端。

## 使用的组件

| 组件 | 用途 |
|------|------|
| Row | 水平布局容器，justify 为 spaceBetween，align 为 center |
| Text (left_text) | 左侧文本，variant 为 body |
| Text (right_text) | 右侧文本，variant 为 caption |

## 消息流程

1. **createSurface** — 创建界面 `example_2`
2. **updateComponents** — 设置 Row 为根组件，包含两个 Text 子组件

## 示例关键片段

```json
{
  "id": "root",
  "component": "Row",
  "children": ["left_text", "right_text"],
  "justify": "spaceBetween",
  "align": "center"
}
```
