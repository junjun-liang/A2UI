# 13_coffee-order.json 说明文档

## 文件概述

本文件展示了咖啡订单卡片，演示了订单明细、小计/税费/总计的表格化布局。

## UI 场景

展示咖啡订单界面，包含店铺名称、订单项目（名称+规格+价格）、小计/税费/总计和操作按钮。

## 使用的组件

| 组件 | 用途 |
|------|------|
| Card | 卡片容器 |
| Column, Row | 布局 |
| Icon (favorite) | 店铺图标 |
| Text | 店铺名、项目名、规格、价格、小计等 |
| Divider | 分隔线 |
| Button | 购买和加入购物车按钮 |

## 消息流程

1. **createSurface** — 创建界面
2. **updateComponents** — 构建订单布局
3. **updateDataModel** — 填充订单数据
