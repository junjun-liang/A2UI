# 16_workout-summary.json 说明文档

## 文件概述

本文件展示了运动摘要卡片，演示了 Icon 的动态绑定和指标三列展示。

## UI 场景

展示运动完成摘要，包含运动图标、标题、三项指标（时长/卡路里/距离）和日期。

## 使用的组件

| 组件 | 用途 |
|------|------|
| Card | 卡片容器 |
| Column, Row | 布局 |
| Icon | 运动图标，name 通过数据绑定 |
| Text | 标题、指标值和标签、日期 |
| Divider | 分隔线 |

## 消息流程

1. **createSurface** — 创建界面
2. **updateComponents** — 构建运动摘要布局
3. **updateDataModel** — 填充运动数据
