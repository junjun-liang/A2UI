# 26_podcast-episode.json

## 文件概述

播客节目卡片示例，展示横向布局中图片与内容的组合。

## UI 场景

播客节目卡片，横向布局显示节目封面、节目名（Tech Talk Daily）、单集标题（The Future of AI in Product Design）、时长和日期、描述，以及 "Play Episode" 按钮。

## 使用的组件

- **Card** - 外层卡片容器
- **Column** - 垂直布局（内容列）
- **Row** - 水平布局（主行：封面+内容，start 对齐）
- **Text** - 节目名（caption）、单集标题（h4）、时长/日期（caption）、描述（body）
- **Image** - 节目封面，fit: cover
- **Button** - Play Episode 按钮

## 消息序列

1. `surfaceUpdate` - 定义组件树
2. `dataModelUpdate` - 填充播客数据
3. `beginRendering` - 开始渲染
