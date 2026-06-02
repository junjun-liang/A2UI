# 19_software-purchase.json 说明文档

## 文件概述

本文件展示了软件购买卡片，演示了选项行和总价展示的表单式布局。

## UI 场景

展示软件许可证购买界面，包含产品名称、座位数、计费周期、总价和确认/取消按钮。

## 使用的组件

| 组件 | 用途 |
|------|------|
| Card | 卡片容器 |
| Column, Row | 布局 |
| Text | 产品名、选项、总价 |
| Divider | 分隔线 |
| Button | 确认和取消按钮 |

## 消息流程

1. **createSurface** — 创建界面
2. **updateComponents** — 构建购买界面布局
3. **updateDataModel** — 填充产品数据
