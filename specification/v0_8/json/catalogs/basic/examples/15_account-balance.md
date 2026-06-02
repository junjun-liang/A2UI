# 15_account-balance.json

## 文件概述

账户余额卡片示例，展示金融信息的简洁展示和操作按钮。

## UI 场景

银行账户余额卡片，显示账户图标、账户名（Primary Checking）、余额（$12,458.32）、更新时间，以及 "Transfer" 和 "Pay Bill" 按钮。

## 使用的组件

- **Card** - 外层卡片容器
- **Column** - 垂直布局（主列）
- **Row** - 水平布局（标题行、操作按钮行）
- **Text** - 账户名（h4）、余额（h1）、更新时间（caption）
- **Icon** - 账户图标（payment）
- **Divider** - 分隔线
- **Button** - Transfer 和 Pay Bill 按钮

## 消息序列

1. `surfaceUpdate` - 定义组件树
2. `dataModelUpdate` - 填充账户数据
3. `beginRendering` - 开始渲染
