# 23_step-counter.json 说明文档

## 文件概述

本文件展示了步数统计卡片，演示了健康数据的展示和双列统计布局。

## UI 场景

展示每日步数统计，包含步数、目标完成百分比、距离和卡路里消耗。

## 使用的组件

| 组件 | 用途 |
|------|------|
| Card | 卡片容器 |
| Column, Row | 布局 |
| Icon (person) | 步数图标 |
| Text | 步数、目标、距离、卡路里 |
| Divider | 分隔线 |

## 消息流程

1. **createSurface** — 创建界面
2. **updateComponents** — 构建步数统计布局
3. **updateDataModel** — 填充步数数据
