# 4_login_form.json

## 文件概述

展示登录表单的示例，演示 TextField、数据模型绑定和 action context 的配合使用。

## UI 场景

一个登录表单，包含标题 "Login"、用户名输入框、密码输入框（obscured 类型）和 "Sign In" 按钮。按钮的 action context 将用户名和密码通过数据模型路径传递给服务端。

## 使用的组件

- **Column** - 垂直布局，distribution: start，alignment: stretch
- **Text** - 表单标题（h2）和按钮标签
- **TextField** - 用户名输入框（shortText）和密码输入框（obscured），通过 path 绑定数据模型
- **Button** - 主要操作按钮，action 包含 context 传递 /username 和 /password

## 消息序列

1. `dataModelUpdate` - 初始化数据模型（username 和 password 为空字符串）
2. `surfaceUpdate` - 定义表单组件树
3. `beginRendering` - 开始渲染

## 示例片段

```json
{
  "id": "submit_button",
  "component": {
    "Button": {
      "child": "submit_label",
      "primary": true,
      "action": {
        "name": "login_submitted",
        "context": [
          { "key": "user", "value": { "path": "/username" } },
          { "key": "pass", "value": { "path": "/password" } }
        ]
      }
    }
  }
}
```
