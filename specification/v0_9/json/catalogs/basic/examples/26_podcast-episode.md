# 26_podcast-episode.json 说明文档

## 文件概述

本文件展示了播客剧集卡片，演示了 Row 横向布局中 Image + 内容的并排展示。

## UI 场景

展示播客剧集信息，包含封面图、节目名、剧集标题、时长、日期、描述和播放按钮。封面图和文字信息横向排列。

## 使用的组件

| 组件 | 用途 |
|------|------|
| Card | 卡片容器 |
| Column, Row | 布局 |
| Image | 播客封面 |
| Text | 节目名、标题、时长、日期、描述 |
| Button | 播放按钮 |

## 消息流程

1. **createSurface** — 创建界面
2. **updateComponents** — 构建播客卡片布局
3. **updateDataModel** — 填充播客数据
