# 29_movie-card.json 说明文档

## 文件概述

本文件展示了电影卡片，演示了 Image + 内容的双段式布局和评分/时长信息展示。

## UI 场景

展示电影信息卡片，包含海报、标题+年份、类型、评分（带星标图标）和时长。

## 使用的组件

| 组件 | 用途 |
|------|------|
| Card | 卡片容器 |
| Column, Row | 布局 |
| Image | 电影海报 |
| Icon (star, calendarToday) | 评分和时长图标 |
| Text | 标题、年份、类型、评分、时长 |

## 消息流程

1. **createSurface** — 创建界面
2. **updateComponents** — 构建电影卡片布局
3. **updateDataModel** — 填充电影数据
