# 13_coffee-order.json

## 文件概述

咖啡订单卡片示例，展示订单明细、价格汇总和操作按钮的组合。

## UI 场景

咖啡订单界面，显示店名 "Sunrise Coffee"、两件商品（Oat Milk Latte 和 Chocolate Croissant）的名称/规格/价格、小计/税费/总计，以及 "Purchase" 和 "Add to cart" 按钮。

## 使用的组件

- **Card** - 外层卡片容器
- **Column** - 垂直布局（主列、商品列、汇总列）
- **Row** - 水平布局（标题行、商品行、价格行、操作行）
- **Text** - 店名（h3）、商品名（body）、规格/标签（caption）、价格（body/h4）
- **Icon** - 咖啡图标（favorite）
- **Divider** - 分隔线
- **Button** - Purchase 和 Add to cart 按钮

## 消息序列

1. `surfaceUpdate` - 定义组件树
2. `dataModelUpdate` - 填充订单数据（使用 valueMap 嵌套商品信息）
3. `beginRendering` - 开始渲染
