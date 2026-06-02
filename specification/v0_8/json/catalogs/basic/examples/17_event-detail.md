# 17_event-detail.json

## 文件概述

活动详情卡片示例，展示 Icon 与文本的行内组合和确认/拒绝交互。

## UI 场景

活动详情卡片，显示活动标题 "Product Launch Meeting"、时间（带日历图标）、地点（带位置图标）、描述，以及 "Accept" 和 "Decline" 按钮。

## 使用的组件

- **Card** - 外层卡片容器
- **Column** - 垂直布局（主列）
- **Row** - 水平布局（时间行、地点行、操作按钮行，带 center 对齐）
- **Text** - 标题（h2）、时间/地点/描述（body）
- **Icon** - 日历图标（calendarToday）、位置图标（locationOn）
- **Divider** - 分隔线
- **Button** - Accept 和 Decline 按钮

## 消息序列

1. `surfaceUpdate` - 定义组件树
2. `dataModelUpdate` - 填充活动数据
3. `beginRendering` - 开始渲染
