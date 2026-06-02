# 25_contact-card.json

## 文件概述

联系人卡片示例，展示头像、联系信息和操作按钮的组合。

## UI 场景

联系人卡片，显示头像、姓名（David Park）、职位（Engineering Manager）、电话、邮箱、地址，以及 "Call" 和 "Message" 按钮。

## 使用的组件

- **Card** - 外层卡片容器
- **Column** - 垂直布局（主列、联系信息列，center 对齐）
- **Row** - 水平布局（每行联系信息、操作按钮行，center 对齐）
- **Text** - 姓名（h2）、职位（body）、联系信息（body）、标签（caption）
- **Image** - 头像，fit: cover，usageHint: avatar
- **Icon** - 电话图标（phone）、邮件图标（mail）、位置图标（locationOn）
- **Divider** - 分隔线
- **Button** - Call 和 Message 按钮

## 消息序列

1. `surfaceUpdate` - 定义组件树
2. `dataModelUpdate` - 填充联系人数据
3. `beginRendering` - 开始渲染
