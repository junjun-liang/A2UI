# 2_row_layout.json

## 文件概述

展示使用 Row 组件进行水平布局的示例。

## UI 场景

左右两段文本水平排列，左侧为 body 样式的 "Left Content"，右侧为 caption 样式的 "Right Content"，使用 spaceBetween 分布和 center 对齐。

## 使用的组件

- **Row** - 水平布局容器，distribution: spaceBetween，alignment: center
- **Text** - 两段文本，分别使用 body 和 caption 样式

## 消息序列

1. `surfaceUpdate` - 定义 Row 包含两个 Text 子组件
2. `beginRendering` - 开始渲染

## 示例片段

```json
{
  "id": "root",
  "component": {
    "Row": {
      "children": { "explicitList": ["left_text", "right_text"] },
      "distribution": "spaceBetween",
      "alignment": "center"
    }
  }
}
```
