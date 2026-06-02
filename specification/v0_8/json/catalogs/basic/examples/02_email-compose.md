# 02_email-compose.json

## 文件概述

邮件撰写卡片示例，展示文本展示与操作按钮的组合。

## UI 场景

邮件撰写界面，显示发件人、收件人、主题、正文内容，以及 "Send email" 和 "Discard" 两个操作按钮。

## 使用的组件

- **Card** - 外层卡片容器
- **Column** - 垂直布局（主列、消息区）
- **Row** - 水平布局（发件人/收件人/主题行、操作按钮行）
- **Text** - 邮件各字段文本（caption/body）
- **Divider** - 分隔线
- **Button** - Send 和 Discard 按钮

## 消息序列

1. `surfaceUpdate` - 定义组件树
2. `dataModelUpdate` - 填充邮件数据
3. `beginRendering` - 开始渲染
