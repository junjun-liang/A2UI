# 04_weather-current.json 说明文档

## 文件概述

本文件展示了当前天气卡片，演示了数组索引路径的数据绑定（如 `/forecast/0/icon`）。

## UI 场景

展示当前天气信息，包含高低温、位置、描述和5天天气预报。

## 使用的组件

| 组件 | 用途 |
|------|------|
| Card | 卡片容器 |
| Column, Row | 布局 |
| Text | 温度、位置、描述、预报数据 |

## 消息流程

1. **createSurface** — 创建界面
2. **updateComponents** — 构建天气布局，预报使用数组索引路径
3. **updateDataModel** — 填充天气数据

## 示例关键片段

```json
{
  "id": "day1-icon",
  "component": "Text",
  "text": { "path": "/forecast/0/icon" },
  "variant": "h3"
}
```
