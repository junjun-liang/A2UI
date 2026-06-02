# 27_stats-card.json

## 文件概述

统计指标卡片示例，展示简洁的指标展示和趋势信息。

## UI 场景

统计指标卡片，显示指标图标、指标名称（Monthly Revenue）、指标值（$48,294），以及趋势图标和趋势文本（+12.5% from last month）。

## 使用的组件

- **Card** - 外层卡片容器
- **Column** - 垂直布局（主列）
- **Row** - 水平布局（标题行、趋势行，center 对齐）
- **Text** - 指标名（caption）、指标值（h1）、趋势文本（body）
- **Icon** - 指标图标（path 绑定 /icon）、趋势图标（path 绑定 /trendIcon）

## 消息序列

1. `surfaceUpdate` - 定义组件树
2. `dataModelUpdate` - 填充统计数据
3. `beginRendering` - 开始渲染
