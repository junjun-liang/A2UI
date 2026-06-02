# 20_restaurant-card.json 说明文档

## 文件概述

本文件展示了餐厅卡片，演示了 Image + 内容的双段式卡片布局和评分展示。

## UI 场景

展示餐厅信息卡片，包含餐厅图片、名称、价格范围、菜系、评分（带星标图标）和距离/配送时间。

## 使用的组件

| 组件 | 用途 |
|------|------|
| Card | 卡片容器 |
| Column, Row | 布局 |
| Image | 餐厅图片 |
| Icon (star) | 评分星标 |
| Text | 餐厅名、价格、菜系、评分、距离 |

## 消息流程

1. **createSurface** — 创建界面
2. **updateComponents** — 构建餐厅卡片布局
3. **updateDataModel** — 填充餐厅数据
