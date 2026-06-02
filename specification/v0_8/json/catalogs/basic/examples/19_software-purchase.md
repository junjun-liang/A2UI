# 19_software-purchase.json

## 文件概述

软件购买卡片示例，展示订单确认和价格明细布局。

## UI 场景

软件许可证购买界面，显示产品名（Design Suite Pro）、席位数（10 seats）、计费周期（Annual）、总价（$1,188/year），以及 "Confirm Purchase" 和 "Cancel" 按钮。

## 使用的组件

- **Card** - 外层卡片容器
- **Column** - 垂直布局（主列、选项列）
- **Row** - 水平布局（席位行、计费行、总价行、操作按钮行，spaceBetween 分布）
- **Text** - 标题（h3）、产品名（h2）、标签（body/h4）、总价（h2）
- **Divider** - 分隔线（两条）
- **Button** - Confirm Purchase 和 Cancel 按钮

## 消息序列

1. `surfaceUpdate` - 定义组件树
2. `dataModelUpdate` - 填充购买数据
3. `beginRendering` - 开始渲染
