# 20_restaurant-card.json

## 文件概述

餐厅卡片示例，展示图片与评分/详情的组合布局。

## UI 场景

餐厅信息卡片，显示餐厅图片、名称（The Italian Kitchen）、价格范围（$$$）、菜系（Italian • Pasta • Wine Bar）、星级评分（4.8）、评论数、距离和配送时间。

## 使用的组件

- **Card** - 外层卡片容器
- **Column** - 垂直布局（主列、内容列）
- **Row** - 水平布局（名称行、评分行、详情行）
- **Text** - 餐厅名（h3）、价格/评分（body）、菜系/评论/距离/配送（caption）
- **Image** - 餐厅图片，fit: cover
- **Icon** - 星级图标（star）

## 消息序列

1. `surfaceUpdate` - 定义组件树
2. `dataModelUpdate` - 填充餐厅数据
3. `beginRendering` - 开始渲染
