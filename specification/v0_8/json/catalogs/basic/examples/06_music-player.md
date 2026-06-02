# 06_music-player.json

## 文件概述

音乐播放器卡片示例，展示 Slider、AudioPlayer 相关组件和 Icon 按钮的组合。

## UI 场景

音乐播放器界面，显示专辑封面、歌曲标题（Blinding Lights）、艺术家、播放进度滑块、当前/总时长，以及上一曲/播放暂停/下一曲控制按钮。

## 使用的组件

- **Card** - 外层卡片容器
- **Column** - 垂直布局（主列、曲目信息、控制区）
- **Row** - 水平布局（时间行、控制按钮行）
- **Text** - 歌曲标题、艺术家、时间（h3/caption）
- **Image** - 专辑封面，fit: cover
- **Slider** - 播放进度，maxValue: 1
- **Icon** - 上一曲（arrowBack）、播放/暂停（path 绑定）、下一曲（arrowForward）
- **Button** - 控制按钮

## 消息序列

1. `surfaceUpdate` - 定义组件树
2. `dataModelUpdate` - 填充播放数据（progress 为 0.45）
3. `beginRendering` - 开始渲染
