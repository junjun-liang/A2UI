# 28_countdown-timer.json

## 文件概述

倒计时卡片示例，展示三列数字+标签的倒计时布局。

## UI 场景

产品发布倒计时卡片，显示事件名称（Product Launch）、天/时/分三列倒计时数字（14 天 08 小时 32 分钟），以及目标日期（January 15, 2025）。

## 使用的组件

- **Card** - 外层卡片容器
- **Column** - 垂直布局（主列、每列时间，center 对齐）
- **Row** - 水平布局（倒计时行，spaceAround 分布）
- **Text** - 事件名（h3）、数字（h1）、标签（caption）、目标日期（body）

## 消息序列

1. `surfaceUpdate` - 定义组件树
2. `dataModelUpdate` - 填充倒计时数据
3. `beginRendering` - 开始渲染
