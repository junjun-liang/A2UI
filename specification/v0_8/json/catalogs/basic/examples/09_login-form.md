# 09_login-form.json

## 文件概述

登录表单卡片示例，展示 TextField 的 obscured 类型和完整表单布局。

## UI 场景

登录表单，包含 "Welcome back" 标题、邮箱输入框、密码输入框（obscured 类型）、"Sign in" 按钮、分隔线，以及 "Don't have an account? Sign up" 链接。

## 使用的组件

- **Card** - 外层卡片容器
- **Column** - 垂直布局（主列、标题列）
- **Row** - 水平布局（注册提示行）
- **Text** - 标题（h2）、副标题（caption）、提示文本（caption）
- **TextField** - 邮箱输入框、密码输入框（obscured）
- **Button** - Sign in 和 Sign up 按钮
- **Divider** - 分隔线

## 消息序列

1. `surfaceUpdate` - 定义组件树
2. `dataModelUpdate` - 初始化空数据
3. `beginRendering` - 开始渲染
