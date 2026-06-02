# 29_movie-card.json

## 文件概述

电影卡片示例，展示海报图片与电影详情的组合。

## UI 场景

电影信息卡片，显示电影海报、标题（Interstellar）、年份（2014）、类型（Sci-Fi • Adventure • Drama）、评分（8.7/10，带星级图标）和时长（2h 49min）。

## 使用的组件

- **Card** - 外层卡片容器
- **Column** - 垂直布局（主列、内容列）
- **Row** - 水平布局（标题行、评分行、时长行，center 对齐）
- **Text** - 电影名（h3）、年份/类型/时长（caption）、评分（body）
- **Image** - 电影海报，fit: cover
- **Icon** - 星级图标（star）、日历图标（calendarToday）

## 消息序列

1. `surfaceUpdate` - 定义组件树
2. `dataModelUpdate` - 填充电影数据
3. `beginRendering` - 开始渲染
