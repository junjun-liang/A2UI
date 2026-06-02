# 14_sports-player.json 说明文档

## 文件概述

本文件展示了运动员卡片，演示了 Image 组件和统计数据的三列展示。

## UI 场景

展示运动员资料卡片，包含照片、姓名、号码、球队和三项统计数据（PPG/RPG/APG）。

## 使用的组件

| 组件 | 用途 |
|------|------|
| Card | 卡片容器 |
| Column, Row | 布局 |
| Image | 运动员照片 |
| Text | 姓名、号码、球队、统计数据 |
| Divider | 分隔线 |

## 消息流程

1. **createSurface** — 创建界面
2. **updateComponents** — 构建运动员卡片布局
3. **updateDataModel** — 填充运动员数据
