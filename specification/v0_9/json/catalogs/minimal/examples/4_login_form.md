# 4_login_form.json 说明文档

## 文件概述

本文件展示了如何使用 TextField 和 Button 组件构建一个完整的登录表单，包括数据绑定和事件上下文传递。

## UI 场景

展示一个登录表单界面，包含用户名输入框、密码输入框和提交按钮。输入框的值通过数据绑定路径关联到数据模型，提交时将用户名和密码作为事件上下文发送给服务端。界面启用了 `sendDataModel` 功能。

## 使用的组件

| 组件 | 用途 |
|------|------|
| Column | 垂直布局容器 |
| Text (form_title) | 表单标题 "Login"，variant 为 h2 |
| TextField (username_field) | 用户名输入框，绑定到 `/username`，variant 为 shortText |
| TextField (password_field) | 密码输入框，绑定到 `/password`，variant 为 obscured |
| Button (submit_button) | 提交按钮，variant 为 primary，触发 login_submitted 事件 |
| Text (submit_label) | 按钮标签 "Sign In" |

## 消息流程

1. **createSurface** — 创建界面 `example_4`，启用 sendDataModel
2. **updateComponents** — 设置包含表单标题、输入框和提交按钮的组件树

## 示例关键片段

```json
{
  "id": "submit_button",
  "component": "Button",
  "child": "submit_label",
  "variant": "primary",
  "action": {
    "event": {
      "name": "login_submitted",
      "context": {
        "user": { "path": "/username" },
        "pass": { "path": "/password" }
      }
    }
  }
}
```
