# 16_workout-summary.json

## 文件概述

运动总结卡片示例，展示三列指标统计布局。

## UI 场景

运动完成总结卡片，显示运动图标、标题 "Workout Complete"、三项指标（时长 32:15、卡路里 385、距离 5.2 km），以及运动日期。

## 使用的组件

- **Card** - 外层卡片容器
- **Column** - 垂直布局（主列、指标列）
- **Row** - 水平布局（标题行、指标行，spaceAround 分布）
- **Text** - 标题（h3）、指标值（h3）、指标标签（caption）、日期（caption）
- **Icon** - 运动图标（path 绑定 /icon）
- **Divider** - 分隔线

## 消息序列

1. `surfaceUpdate` - 定义组件树
2. `dataModelUpdate` - 填充运动数据
3. `beginRendering` - 开始渲染
