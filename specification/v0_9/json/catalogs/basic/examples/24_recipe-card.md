# 24_recipe-card.json 说明文档

## 文件概述

本文件展示了食谱卡片，演示了 Image + 内容的双段式布局和 Icon 与文本的组合。

## UI 场景

展示食谱卡片，包含食物图片、标题、评分（带星标）、准备时间和烹饪时间。

## 使用的组件

| 组件 | 用途 |
|------|------|
| Card | 卡片容器 |
| Column, Row | 布局 |
| Image | 食物图片 |
| Icon (star, calendarToday, warning) | 评分、时间图标 |
| Text | 标题、评分、时间、份数 |

## 消息流程

1. **createSurface** — 创建界面
2. **updateComponents** — 构建食谱卡片布局
3. **updateDataModel** — 填充食谱数据
