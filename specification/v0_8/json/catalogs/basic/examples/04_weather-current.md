# 04_weather-current.json

## 文件概述

当前天气卡片示例，展示多数据项和重复结构。

## UI 场景

天气信息卡片，显示当前高低温（72°/58°）、位置（Austin, TX）、天气描述，以及 5 天预报（含图标和温度）。

## 使用的组件

- **Card** - 外层卡片容器
- **Column** - 垂直布局（主列、每日预报列）
- **Row** - 水平布局（温度行、预报行）
- **Text** - 温度、位置、描述、预报数据（h1/h2/h3/caption）

## 消息序列

1. `surfaceUpdate` - 定义组件树
2. `dataModelUpdate` - 填充天气数据
3. `beginRendering` - 开始渲染
