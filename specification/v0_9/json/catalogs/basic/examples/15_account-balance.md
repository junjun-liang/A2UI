# 15_account-balance.json 说明文档

## 文件概述

本文件展示了账户余额卡片，演示了金融数据的展示和双操作按钮布局。

## UI 场景

展示银行账户余额界面，包含账户名称、余额、更新时间和转账/缴费按钮。

## 使用的组件

| 组件 | 用途 |
|------|------|
| Card | 卡片容器 |
| Column, Row | 布局 |
| Icon (payment) | 账户图标 |
| Text | 账户名、余额、更新时间 |
| Divider | 分隔线 |
| Button | 转账和缴费按钮 |

## 消息流程

1. **createSurface** — 创建界面
2. **updateComponents** — 构建账户余额布局
3. **updateDataModel** — 填充账户数据
