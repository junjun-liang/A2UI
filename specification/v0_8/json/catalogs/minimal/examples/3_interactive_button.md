# 3_interactive_button.json

## 文件概述

展示交互式按钮的示例，演示 Button 组件与 action 的配合使用。

## UI 场景

垂直布局中包含一段提示文本 "Click the button below" 和一个主要操作按钮 "Click Me"，点击按钮触发 "button_clicked" 动作。

## 使用的组件

- **Column** - 垂直布局，distribution: center，alignment: center
- **Text** - 提示文本（body）和按钮标签文本
- **Button** - 主要操作按钮（primary: true），action.name: "button_clicked"

## 消息序列

1. `surfaceUpdate` - 定义 Column 包含 Text 和 Button
2. `beginRendering` - 开始渲染

## 示例片段

```json
{
  "id": "action_button",
  "component": {
    "Button": {
      "child": "button_label",
      "primary": true,
      "action": { "name": "button_clicked" }
    }
  }
}
```
