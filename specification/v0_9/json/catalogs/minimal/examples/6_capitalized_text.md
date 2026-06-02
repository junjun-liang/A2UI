# 6_capitalized_text.json 说明文档

## 文件概述

本文件展示了如何使用客户端函数（capitalize）实现动态文本转换，演示了数据模型与函数调用的结合。

## UI 场景

展示一个文本转换界面：用户在输入框中输入小写文本，下方实时显示通过 `capitalize` 函数转换后的大写文本。输入框的值绑定到数据模型的 `/inputValue` 路径，输出文本通过函数调用动态生成。

## 使用的组件

| 组件 | 用途 |
|------|------|
| Column | 垂直布局容器 |
| TextField (input_field) | 输入框，绑定到 `/inputValue`，用于输入小写文本 |
| Text (result_label) | 结果标签 "Capitalized output:"，variant 为 caption |
| Text (result_text) | 结果文本，通过 capitalize 函数动态生成，variant 为 h2 |

## 消息流程

1. **createSurface** — 创建界面 `example_6`，启用 sendDataModel
2. **updateComponents** — 设置包含输入框和函数绑定输出文本的组件树

## 示例关键片段

```json
{
  "id": "result_text",
  "component": "Text",
  "text": {
    "call": "capitalize",
    "args": {
      "value": { "path": "/inputValue" }
    },
    "returnType": "string"
  },
  "variant": "h2"
}
```

此示例展示了 DynamicString 的函数调用形式：`text` 字段不是字符串字面量，而是一个函数调用对象，客户端会实时执行该函数并显示结果。
