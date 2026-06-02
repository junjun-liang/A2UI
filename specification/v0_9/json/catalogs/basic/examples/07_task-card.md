# 07_task-card.json 说明文档

## 文件概述

本文件展示了任务卡片，演示了 Row 嵌套 Column 的信息密度布局和 Icon 的动态绑定。

## UI 场景

展示一个任务卡片，包含任务标题、描述、截止日期、项目标签和优先级图标。

## 使用的组件

| 组件 | 用途 |
|------|------|
| Card | 卡片容器 |
| Column, Row | 布局 |
| Icon | 优先级图标，name 通过数据绑定 |
| Text | 标题、描述、日期、项目 |

## 消息流程

1. **createSurface** — 创建界面
2. **updateComponents** — 构建任务卡片布局
3. **updateDataModel** — 填充任务数据
