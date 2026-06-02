# 12_chat-message.json

## 文件概述

聊天消息卡片示例，展示多用户消息列表和头像显示。

## UI 场景

聊天频道界面，显示频道名称 "project-updates" 和两条消息，每条消息包含用户头像、用户名、时间和消息内容。

## 使用的组件

- **Card** - 外层卡片容器
- **Column** - 垂直布局（主列、消息列、消息内容列）
- **Row** - 水平布局（频道头行、消息行、消息头行）
- **Text** - 频道名（h3）、用户名（h4）、时间（caption）、消息文本（body）
- **Image** - 用户头像，fit: cover，usageHint: avatar
- **Icon** - 频道图标（info）
- **Divider** - 分隔线

## 消息序列

1. `surfaceUpdate` - 定义组件树
2. `dataModelUpdate` - 填充聊天数据（使用 valueMap 嵌套消息结构）
3. `beginRendering` - 开始渲染

## 示例片段

```json
{
  "key": "message1",
  "valueMap": [
    { "key": "avatar", "valueString": "https://..." },
    { "key": "username", "valueString": "Mike Chen" },
    { "key": "time", "valueString": "10:32 AM" },
    { "key": "text", "valueString": "Just pushed the new API changes." }
  ]
}
```
