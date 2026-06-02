# 05_product-card.json 说明文档

## 文件概述

本文件展示了商品卡片，演示了 Image 组件和价格展示布局。

## UI 场景

展示一个商品卡片，包含商品图片、名称、评分、原价/现价和加入购物车按钮。

## 使用的组件

| 组件 | 用途 |
|------|------|
| Card | 卡片容器 |
| Column, Row | 布局 |
| Image | 商品图片，fit 为 cover |
| Text | 商品名、评分、价格 |
| Button | 加入购物车按钮 |

## 消息流程

1. **createSurface** — 创建界面
2. **updateComponents** — 构建商品卡片布局
3. **updateDataModel** — 填充商品数据
