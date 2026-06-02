# 23_step-counter.json

## 文件概述

步数统计卡片示例，展示健康数据的两列指标布局。

## UI 场景

今日步数统计卡片，显示步数（8,432）、目标进度（84% of 10,000 goal），以及距离（3.8 mi）和卡路里（312）两项辅助指标。

## 使用的组件

- **Card** - 外层卡片容器
- **Column** - 垂直布局（主列、指标列，center 对齐）
- **Row** - 水平布局（标题行、指标行，spaceAround 分布）
- **Text** - 标题（h4）、步数（h1）、进度（body）、指标值（h3）、指标标签（caption）
- **Icon** - 人物图标（person）
- **Divider** - 分隔线

## 消息序列

1. `surfaceUpdate` - 定义组件树
2. `dataModelUpdate` - 填充步数数据
3. `beginRendering` - 开始渲染
