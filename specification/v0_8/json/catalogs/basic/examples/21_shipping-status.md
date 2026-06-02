# 21_shipping-status.json

## 文件概述

物流状态卡片示例，展示步骤进度和当前状态高亮。

## UI 场景

包裹物流追踪卡片，显示追踪号、四个步骤（Order Placed ✓ → Shipped ✓ → Out for Delivery（当前）→ Delivered），以及预计送达时间。

## 使用的组件

- **Card** - 外层卡片容器
- **Column** - 垂直布局（主列、步骤列）
- **Row** - 水平布局（标题行、每个步骤行、预计送达行，center 对齐）
- **Text** - 标题（h3）、追踪号（caption）、步骤文本（body/h4/caption）、预计送达（body）
- **Icon** - 包裹图标（info）、完成步骤图标（check）、当前步骤图标（path 绑定）、日历图标（calendarToday）
- **Divider** - 分隔线

## 消息序列

1. `surfaceUpdate` - 定义组件树
2. `dataModelUpdate` - 填充物流数据
3. `beginRendering` - 开始渲染
