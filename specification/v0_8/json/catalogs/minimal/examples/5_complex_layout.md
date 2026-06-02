# 5_complex_layout.json

## 文件概述

展示复杂嵌套布局的示例，演示 Row 和 Column 的嵌套组合以及 weight 属性的使用。

## UI 场景

一个用户资料表单，包含标题 "User Profile Form"（h1）、水平排列的 First Name 和 Last Name 输入框（各占等宽 weight: 1）、以及底部提示文本 "Please fill out all fields."（caption）。

## 使用的组件

- **Column** - 外层垂直布局，distribution: spaceBetween，alignment: stretch
- **Row** - 内层水平布局，包含两个等宽输入框
- **Text** - 标题（h1）和提示文本（caption）
- **TextField** - First Name 和 Last Name 输入框，通过 path 绑定数据模型，使用 weight: 1 实现等宽

## 消息序列

1. `surfaceUpdate` - 定义嵌套布局组件树
2. `beginRendering` - 开始渲染

## 示例片段

```json
{
  "id": "first_name",
  "weight": 1,
  "component": {
    "TextField": {
      "label": { "literalString": "First Name" },
      "text": { "path": "/firstName" }
    }
  }
}
```
