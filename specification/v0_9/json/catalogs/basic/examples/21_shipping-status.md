# 21_shipping-status.json 说明文档

## 文件概述

本文件展示了物流状态卡片，演示了步骤式进度展示和 Icon 的动态绑定（当前步骤图标）。

## UI 场景

展示包裹物流状态，包含4个步骤（已下单→已发货→配送中→已送达），当前步骤高亮显示，图标通过数据绑定动态切换。

## 使用的组件

| 组件 | 用途 |
|------|------|
| Card | 卡片容器 |
| Column, Row | 布局 |
| Icon (check, send, calendarToday) | 步骤图标 |
| Text | 步骤文本、追踪号、预计送达 |
| Divider | 分隔线 |

## 示例关键片段

```json
{
  "id": "step3-icon",
  "component": "Icon",
  "name": { "path": "/currentStepIcon" }
}
```

当前步骤的图标通过数据绑定动态设置，区分进行中和已完成状态。
