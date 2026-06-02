# 25_contact-card.json 说明文档

## 文件概述

本文件展示了联系人卡片，演示了 Image 的 avatar 变体和多行联系信息布局。

## UI 场景

展示联系人信息，包含头像、姓名、职位、电话、邮箱、地址和操作按钮（呼叫/消息）。

## 使用的组件

| 组件 | 用途 |
|------|------|
| Card | 卡片容器 |
| Column, Row | 布局 |
| Image (variant: avatar) | 联系人头像 |
| Icon (phone, mail, locationOn) | 联系方式图标 |
| Text | 姓名、职位、联系方式 |
| Divider | 分隔线 |
| Button | 呼叫和消息按钮 |

## 消息流程

1. **createSurface** — 创建界面
2. **updateComponents** — 构建联系人卡片布局
3. **updateDataModel** — 填充联系人数据
