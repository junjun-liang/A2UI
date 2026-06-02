# 22_credit-card.json

## 文件概述

信用卡展示卡片示例，展示金融卡片信息的紧凑布局。

## UI 场景

信用卡信息展示，显示卡片类型（VISA）、卡号（•••• •••• •••• 4242）、持卡人姓名（SARAH JOHNSON）和到期日（09/27）。

## 使用的组件

- **Card** - 外层卡片容器
- **Column** - 垂直布局（主列、持卡人列、到期列）
- **Row** - 水平布局（类型行、详情行，spaceBetween 分布）
- **Text** - 卡类型（h4）、卡号（h2）、标签（caption）、姓名/到期日（body）
- **Icon** - 支付图标（payment）

## 消息序列

1. `surfaceUpdate` - 定义组件树
2. `dataModelUpdate` - 填充卡片数据
3. `beginRendering` - 开始渲染
