# 08_user-profile.json

## 文件概述

用户资料卡片示例，展示 Image 的 avatar usageHint 和统计数据展示。

## UI 场景

用户资料卡片，显示头像、姓名（Sarah Chen）、用户名（@sarahchen）、个人简介、粉丝/关注/帖子统计，以及 "Follow" 按钮。

## 使用的组件

- **Card** - 外层卡片容器
- **Column** - 垂直布局（主列、信息列、统计列）
- **Row** - 水平布局（统计行）
- **Text** - 姓名（h2）、用户名（caption）、简介（body）、统计数字（h3）和标签（caption）
- **Image** - 头像，fit: cover，usageHint: avatar
- **Button** - Follow 按钮

## 消息序列

1. `surfaceUpdate` - 定义组件树
2. `dataModelUpdate` - 填充用户数据
3. `beginRendering` - 开始渲染
