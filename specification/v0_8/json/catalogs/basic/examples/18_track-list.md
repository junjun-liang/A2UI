# 18_track-list.json

## 文件概述

音乐曲目列表卡片示例，展示多行重复结构和 Image 小图。

## UI 场景

播放列表 "Focus Flow" 的曲目列表，显示 3 首歌曲，每首包含序号、封面小图、标题、艺术家和时长。

## 使用的组件

- **Card** - 外层卡片容器
- **Column** - 垂直布局（主列、曲目列）
- **Row** - 水平布局（标题行、每首曲目的信息行，center 对齐）
- **Text** - 播放列表名（h3）、序号（caption）、曲名（body）、艺术家和时长（caption）
- **Image** - 曲目封面小图，fit: cover
- **Icon** - 播放列表图标（arrowForward）
- **Divider** - 分隔线

## 消息序列

1. `surfaceUpdate` - 定义组件树
2. `dataModelUpdate` - 填充曲目数据（使用 valueMap 嵌套每首曲目信息）
3. `beginRendering` - 开始渲染
