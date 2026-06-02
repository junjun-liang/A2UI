# 05_product-card.json

## 文件概述

商品卡片示例，展示 Image 组件与商品信息的组合。

## UI 场景

商品展示卡片，包含商品图片、名称（Wireless Headphones Pro）、星级评分、评论数、价格（$199.99，原价 $249.99）和 "Add to Cart" 按钮。

## 使用的组件

- **Card** - 外层卡片容器
- **Column** - 垂直布局（主列、详情列）
- **Row** - 水平布局（评分行、价格行、操作行）
- **Text** - 商品名、评分、评论、价格（h2/h3/caption/body）
- **Image** - 商品图片，fit: cover
- **Button** - Add to Cart 按钮

## 消息序列

1. `surfaceUpdate` - 定义组件树
2. `dataModelUpdate` - 填充商品数据
3. `beginRendering` - 开始渲染
