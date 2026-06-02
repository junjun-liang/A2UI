# 14_sports-player.json

## 文件概述

运动员资料卡片示例，展示图片与统计数据的三列布局。

## UI 场景

运动员资料卡片，显示运动员照片、姓名（Marcus Johnson）、号码（#23）、球队（LA Lakers），以及三项统计数据（PPG 28.4、RPG 7.2、APG 6.8）。

## 使用的组件

- **Card** - 外层卡片容器
- **Column** - 垂直布局（主列、信息列、统计列）
- **Row** - 水平布局（详情行、统计行，spaceAround 分布）
- **Text** - 姓名（h2）、号码（h3）、球队（caption）、统计值（h3）、统计标签（caption）
- **Image** - 运动员照片，fit: cover
- **Divider** - 分隔线

## 消息序列

1. `surfaceUpdate` - 定义组件树
2. `dataModelUpdate` - 填充运动员数据（使用 valueMap 嵌套统计数据）
3. `beginRendering` - 开始渲染
