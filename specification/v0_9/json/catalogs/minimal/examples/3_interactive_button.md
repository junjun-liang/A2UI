# 3_interactive_button.json 说明文档

## 文件概述

本文件展示了如何使用 Button 组件创建可交互按钮，并绑定点击事件。

## UI 场景

展示一个包含提示文本和交互按钮的界面。点击 "Click Me" 按钮时，会向服务端发送名为 `button_clicked` 的事件。

## 使用的组件

| 组件 | 用途 |
|------|------|
| Column | 垂直布局容器，居中对齐 |
| Text (title) | 提示文本 "Click the button below" |
| Button | 主按钮，variant 为 primary，绑定 button_clicked 事件 |
| Text (button_label) | 按钮标签文本 "Click Me" |

## 消息流程

1. **createSurface** — 创建界面 `example_3`
2. **updateComponents** — 设置 Column 根组件，包含提示文本和带事件的按钮

## 示例关键片段

```json
{
  "id": "action_button",
  "component": "Button",
  "child": "button_label",
  "variant": "primary",
  "action": {
    "event": {
      "name": "button_clicked",
      "context": {}
    }
  }
}
```
