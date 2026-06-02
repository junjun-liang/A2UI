# 07_task-card.json

## 文件概述

任务卡片示例，展示 Icon 与文本信息的横向组合。

## UI 场景

任务卡片，显示任务标题 "Review pull request"、描述、截止日期（Today）、所属项目（Backend），以及优先级图标。

## 使用的组件

- **Card** - 外层卡片容器
- **Row** - 水平布局（内容+优先级行）
- **Column** - 垂直布局（内容列）
- **Text** - 标题（h3）、描述（body）、截止日期和项目（caption）
- **Icon** - 优先级图标（path 绑定 /priorityIcon）

## 消息序列

1. `surfaceUpdate` - 定义组件树
2. `dataModelUpdate` - 填充任务数据
3. `beginRendering` - 开始渲染
