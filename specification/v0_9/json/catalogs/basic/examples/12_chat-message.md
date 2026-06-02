# 12_chat-message.json 说明文档

## 文件概述

本文件展示了聊天消息卡片，演示了 Image 的 avatar 变体和消息列表布局。

## UI 场景

展示聊天频道界面，包含频道标题和两条消息，每条消息包含头像、用户名、时间和内容。

## 使用的组件

| 组件 | 用途 |
|------|------|
| Card | 卡片容器 |
| Column, Row | 布局 |
| Image (variant: avatar) | 用户头像 |
| Icon (info) | 频道图标 |
| Text | 频道名、用户名、时间、消息内容 |
| Divider | 分隔线 |

## 消息流程

1. **createSurface** — 创建界面
2. **updateComponents** — 构建聊天消息布局
3. **updateDataModel** — 填充频道和消息数据
