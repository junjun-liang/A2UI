# 11_purchase-complete.json

## 文件概述

购买完成卡片示例，展示订单确认信息的组合展示。

## UI 场景

购买完成确认卡片，显示成功图标、标题 "Purchase Complete"、商品图片和名称（Wireless Headphones Pro）、价格、配送日期、卖家信息，以及 "View Order Details" 按钮。

## 使用的组件

- **Card** - 外层卡片容器
- **Column** - 垂直布局（主列、商品信息列、详情列）
- **Row** - 水平布局（商品行、配送行、卖家行）
- **Text** - 标题（h2）、商品名（h4）、价格（body）、配送和卖家信息
- **Image** - 商品图片，fit: cover
- **Icon** - 成功图标（check）、配送图标（arrowForward）
- **Divider** - 分隔线
- **Button** - View Order Details 按钮

## 消息序列

1. `surfaceUpdate` - 定义组件树
2. `dataModelUpdate` - 填充订单数据
3. `beginRendering` - 开始渲染
