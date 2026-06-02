# 10_notification-permission.json 说明文档

## 文件概述

本文件展示了通知权限请求卡片，演示了 Icon 的动态绑定和简洁的双按钮操作。

## UI 场景

展示一个通知权限请求界面，包含图标、标题、描述和是/否按钮。

## 使用的组件

| 组件 | 用途 |
|------|------|
| Card | 卡片容器 |
| Column, Row | 布局 |
| Icon | 通知图标，name 通过数据绑定 |
| Text | 标题和描述 |
| Button | 接受和拒绝按钮 |

## 消息流程

1. **createSurface** — 创建界面
2. **updateComponents** — 构建权限请求布局
3. **updateDataModel** — 填充图标和文本数据
