# 03_calendar-day.json 说明文档

## 文件概述

本文件展示了日历日程卡片，演示了嵌套布局和多个事件项的展示。

## UI 场景

展示某天的日程安排，包含日期显示、3个日程事件和操作按钮（添加到日历/丢弃）。

## 使用的组件

| 组件 | 用途 |
|------|------|
| Card | 卡片容器 |
| Column, Row | 布局 |
| Text | 日期、事件标题和时间 |
| Divider | 分隔线 |
| Button | 添加和丢弃按钮 |

## 消息流程

1. **createSurface** — 创建界面
2. **updateComponents** — 构建日程布局
3. **updateDataModel** — 填充日期和事件数据
