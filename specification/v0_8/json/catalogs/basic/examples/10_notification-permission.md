# 10_notification-permission.json

## 文件概述

通知权限请求卡片示例，展示简单的确认/拒绝交互模式。

## UI 场景

通知权限请求对话框，显示图标、标题 "Enable notification"、描述 "Get alerts for order status changes"，以及 "Yes" 和 "No" 两个按钮。

## 使用的组件

- **Card** - 外层卡片容器
- **Column** - 垂直布局（居中对齐）
- **Row** - 水平布局（按钮行，居中分布）
- **Text** - 标题（h3）、描述（body）
- **Icon** - 通知图标（path 绑定 /icon）
- **Button** - Yes（accept）和 No（decline）按钮

## 消息序列

1. `surfaceUpdate` - 定义组件树
2. `dataModelUpdate` - 填充通知数据
3. `beginRendering` - 开始渲染
