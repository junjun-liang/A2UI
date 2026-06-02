# 01_flight-status.json

## 文件概述

航班状态卡片示例，展示如何构建信息密集型卡片 UI。

## UI 场景

航班信息卡片，显示航班号 OS 87、日期、出发地 Vienna → 目的地 New York、出发/到达时间和状态 "On Time"。

## 使用的组件

- **Card** - 外层卡片容器
- **Column** - 垂直布局（主列、出发/到达/状态列）
- **Row** - 水平布局（标题行、航线行、时间行）
- **Text** - 航班号、日期、城市、时间、状态等文本（h2/h3/caption/body）
- **Icon** - 航班图标（send）
- **Divider** - 分隔线

## 消息序列

1. `surfaceUpdate` - 定义组件树
2. `dataModelUpdate` - 填充航班数据
3. `beginRendering` - 开始渲染

## 示例片段

```json
{
  "id": "route-row",
  "component": {
    "Row": {
      "children": { "explicitList": ["origin", "arrow", "destination"] },
      "alignment": "center"
    }
  }
}
```
