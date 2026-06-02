# 01_flight-status.json 说明文档

## 文件概述

本文件展示了航班状态卡片，是 A2UI basic 目录的典型示例，演示了 Card、Row、Column、Icon、Divider 等组件的组合使用，以及数据绑定和 updateDataModel 的分离式数据填充模式。

## UI 场景

展示一个航班状态卡片，包含航班号、日期、出发地→目的地、出发/到达时间和航班状态。所有动态数据通过数据绑定路径引用，在 updateDataModel 消息中填充。

## 使用的组件

| 组件 | 用途 |
|------|------|
| Card | 卡片容器 |
| Column, Row | 嵌套布局 |
| Icon (send) | 航班指示图标 |
| Text | 航班号、日期、城市、时间、状态等 |
| Divider | 分隔线 |

## 消息流程

1. **createSurface** — 创建界面，启用 sendDataModel
2. **updateComponents** — 构建完整的组件树，所有文本通过 `{path: "..."}` 绑定数据模型
3. **updateDataModel** — 填充航班数据（OS 87, Vienna → New York, On Time）

## 示例关键片段

```json
{
  "id": "route-row",
  "component": "Row",
  "children": ["origin", "arrow", "destination"],
  "align": "center"
}
```
