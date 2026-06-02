# 11_purchase-complete.json 说明文档

## 文件概述

本文件展示了购买完成卡片，演示了 Icon 组件（check、arrowForward）和商品信息展示。

## UI 场景

展示购买完成确认界面，包含成功图标、商品图片和信息、配送日期、卖家信息和查看订单按钮。

## 使用的组件

| 组件 | 用途 |
|------|------|
| Card | 卡片容器 |
| Column, Row | 布局 |
| Icon (check, arrowForward) | 成功和配送图标 |
| Image | 商品图片 |
| Text | 标题、商品名、价格、配送信息 |
| Divider | 分隔线 |
| Button | 查看订单按钮 |

## 消息流程

1. **createSurface** — 创建界面
2. **updateComponents** — 构建购买完成布局
3. **updateDataModel** — 填充商品和配送数据
