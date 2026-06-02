# 02_email-compose.json 说明文档

## 文件概述

本文件展示了邮件撰写界面，演示了多行文本布局和双按钮操作栏。

## UI 场景

展示一封邮件的预览界面，包含发件人、收件人、主题、正文内容和操作按钮（发送/丢弃）。

## 使用的组件

| 组件 | 用途 |
|------|------|
| Card | 卡片容器 |
| Column, Row | 布局 |
| Text | 邮件各字段内容 |
| Divider | 分隔线 |
| Button | 发送和丢弃按钮 |

## 消息流程

1. **createSurface** — 创建界面
2. **updateComponents** — 构建邮件布局
3. **updateDataModel** — 填充邮件数据
