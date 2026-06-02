# 18_track-list.json 说明文档

## 文件概述

本文件展示了音乐曲目列表卡片，演示了 Image 组件在列表项中的使用。

## UI 场景

展示播放列表界面，包含播放列表名称和3首曲目，每首曲目包含编号、封面、标题、艺术家和时长。

## 使用的组件

| 组件 | 用途 |
|------|------|
| Card | 卡片容器 |
| Column, Row | 布局 |
| Icon (play) | 播放列表图标 |
| Image | 曲目封面 |
| Text | 曲目编号、标题、艺术家、时长 |
| Divider | 分隔线 |

## 消息流程

1. **createSurface** — 创建界面
2. **updateComponents** — 构建曲目列表布局
3. **updateDataModel** — 填充曲目数据
