# 08_user-profile.json 说明文档

## 文件概述

本文件展示了用户资料卡片，演示了 Image 的 avatar 变体和统计数据的三列布局。

## UI 场景

展示用户资料卡片，包含头像、姓名、用户名、简介、关注/粉丝/帖子统计和关注按钮。

## 使用的组件

| 组件 | 用途 |
|------|------|
| Card | 卡片容器 |
| Column, Row | 布局 |
| Image (variant: avatar) | 用户头像 |
| Text | 姓名、用户名、简介、统计数据 |
| Button | 关注按钮 |

## 消息流程

1. **createSurface** — 创建界面
2. **updateComponents** — 构建用户资料布局
3. **updateDataModel** — 填充用户数据
