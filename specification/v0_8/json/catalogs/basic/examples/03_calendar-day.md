# 03_calendar-day.json

## 文件概述

日历日程卡片示例，展示 valueMap 数据结构和多事件列表。

## UI 场景

某日日程卡片，显示星期五 28 号的三个日程事件（Lunch、Q1 roadmap review、Team standup），以及 "Add to calendar" 和 "Discard" 按钮。

## 使用的组件

- **Card** - 外层卡片容器
- **Column** - 垂直布局（日期列、事件列）
- **Row** - 水平布局（日期+事件行、操作按钮行）
- **Text** - 日期、事件标题和时间（h1/caption/body）
- **Divider** - 分隔线
- **Button** - Add 和 Discard 按钮

## 消息序列

1. `surfaceUpdate` - 定义组件树
2. `dataModelUpdate` - 填充日程数据（使用 valueMap 嵌套结构）
3. `beginRendering` - 开始渲染

## 示例片段

```json
{
  "key": "event1",
  "valueMap": [
    { "key": "title", "valueString": "Lunch" },
    { "key": "time", "valueString": "12:00 - 12:45 PM" }
  ]
}
```
