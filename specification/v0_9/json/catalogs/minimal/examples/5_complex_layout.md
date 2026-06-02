# 5_complex_layout.json 说明文档

## 文件概述

本文件展示了如何使用嵌套的 Row 和 Column 组件构建复杂的表单布局。

## UI 场景

展示一个用户资料表单界面，包含标题、嵌套的水平布局（名和姓并排）以及底部提示文本。两个 TextField 使用 `weight` 属性实现等宽分配。

## 使用的组件

| 组件 | 用途 |
|------|------|
| Column (root) | 垂直布局容器，justify 为 spaceBetween |
| Text (header) | 标题 "User Profile Form"，variant 为 h1 |
| Row (form_row) | 水平布局容器，包含名和姓输入框 |
| TextField (first_name) | 名输入框，绑定到 `/firstName`，weight 为 1 |
| TextField (last_name) | 姓输入框，绑定到 `/lastName`，weight 为 1 |
| Text (footer) | 底部提示文本，variant 为 caption |

## 消息流程

1. **createSurface** — 创建界面 `example_5`
2. **updateComponents** — 设置嵌套布局的组件树

## 示例关键片段

```json
{
  "id": "form_row",
  "component": "Row",
  "children": ["first_name", "last_name"],
  "justify": "start",
  "align": "start"
}
```

weight 属性使两个输入框等宽分配水平空间。
