# 17_event-detail.json 说明文档

## 文件概述

本文件展示了活动详情卡片，演示了 Icon（calendarToday、locationOn）与文本的组合布局。

## UI 场景

展示活动详情界面，包含标题、时间（带日历图标）、地点（带位置图标）、描述和接受/拒绝按钮。

## 使用的组件

| 组件 | 用途 |
|------|------|
| Card | 卡片容器 |
| Column, Row | 布局 |
| Icon (calendarToday, locationOn) | 时间和地点图标 |
| Text | 标题、时间、地点、描述 |
| Divider | 分隔线 |
| Button | 接受和拒绝按钮 |

## 消息流程

1. **createSurface** — 创建界面
2. **updateComponents** — 构建活动详情布局
3. **updateDataModel** — 填充活动数据
