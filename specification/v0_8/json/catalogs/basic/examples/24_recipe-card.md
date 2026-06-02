# 24_recipe-card.json

## 文件概述

食谱卡片示例，展示图片与多行详情信息的组合。

## UI 场景

食谱卡片，显示菜品图片、名称（Mediterranean Quinoa Bowl）、评分（4.9）和评论数、准备时间（15 min prep）和烹饪时间（20 min cook），以及份量（Serves 4）。

## 使用的组件

- **Card** - 外层卡片容器
- **Column** - 垂直布局（主列、内容列）
- **Row** - 水平布局（评分行、时间行、准备/烹饪行）
- **Text** - 菜名（h3）、评分（body）、评论/时间/份量（caption）
- **Image** - 菜品图片，fit: cover
- **Icon** - 星级图标（star）、日历图标（calendarToday）、烹饪图标（warning）

## 消息序列

1. `surfaceUpdate` - 定义组件树
2. `dataModelUpdate` - 填充食谱数据
3. `beginRendering` - 开始渲染
