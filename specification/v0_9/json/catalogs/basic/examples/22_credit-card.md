# 22_credit-card.json 说明文档

## 文件概述

本文件展示了信用卡卡片，演示了金融卡片的紧凑信息布局。

## UI 场景

展示信用卡信息，包含卡类型、卡号（脱敏）、持卡人姓名和有效期。

## 使用的组件

| 组件 | 用途 |
|------|------|
| Card | 卡片容器 |
| Column, Row | 布局 |
| Icon (payment) | 卡类型图标 |
| Text | 卡类型、卡号、持卡人、有效期 |

## 消息流程

1. **createSurface** — 创建界面
2. **updateComponents** — 构建信用卡布局
3. **updateDataModel** — 填充卡片数据
