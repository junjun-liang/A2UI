# 06_music-player.json 说明文档

## 文件概述

本文件展示了音乐播放器卡片，演示了 Slider 组件和 Icon 作为 Button 子组件的用法。

## UI 场景

展示一个音乐播放器，包含专辑封面、歌曲信息、进度条、时间显示和控制按钮（上一曲/播放暂停/下一曲）。播放按钮的图标通过数据绑定动态切换。

## 使用的组件

| 组件 | 用途 |
|------|------|
| Card | 卡片容器 |
| Column, Row | 布局 |
| Image | 专辑封面 |
| Slider | 播放进度条，max 为 1 |
| Icon | 控制按钮图标（skipPrevious、pause、skipNext） |
| Button | 播放控制按钮 |
| Text | 歌曲标题、艺术家、时间 |

## 示例关键片段

```json
{
  "id": "play-btn-icon",
  "component": "Icon",
  "name": { "path": "/playIcon" }
}
```

Icon 的 name 属性通过数据绑定动态切换播放/暂停图标。
