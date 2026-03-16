# A2UI v0.9 Basic Catalog 与 Common Types 解析

## 文件位置

- **Basic Catalog**: `agent_sdks/python/src/a2ui/assets/0.9/basic_catalog.json`
- **Common Types**: `agent_sdks/python/src/a2ui/assets/0.9/common_types.json`

---

## 概述

这两个文件是 A2UI v0.9 协议的核心定义：

| 文件 | 作用 |
|------|------|
| `basic_catalog.json` | 定义可用的 UI 组件 |
| `common_types.json` | 定义跨组件共享的数据类型 |

---

## Basic Catalog 组件列表

A2UI v0.9 Basic Catalog 包含 **18 个 UI 组件**：

### 布局组件 (Layout)

| 组件 | 说明 |
|------|------|
| `Row` | 水平布局容器 |
| `Column` | 垂直布局容器 |
| `Card` | 卡片容器 |
| `List` | 列表容器 |
| `Tabs` | 标签页容器 |
| `Modal` | 模态对话框 |

### 内容组件 (Content)

| 组件 | 说明 |
|------|------|
| `Text` | 文本显示 |
| `Image` | 图片显示 |
| `Icon` | 图标显示 |
| `Video` | 视频播放 |
| `AudioPlayer` | 音频播放 |
| `Divider` | 分隔线 |

### 交互组件 (Interactive)

| 组件 | 说明 |
|------|------|
| `Button` | 按钮 |
| `TextField` | 文本输入框 |
| `CheckBox` | 复选框 |
| `ChoicePicker` | 选择器 |
| `Slider` | 滑块 |
| `DateTimeInput` | 日期时间输入 |

---

## 核心组件详解

### 1. Text 组件

```json
{
  "Text": {
    "properties": {
      "component": { "const": "Text" },
      "text": { "$ref": "common_types.json#/$defs/DynamicString" },
      "variant": {
        "type": "string",
        "enum": ["h1", "h2", "h3", "h4", "h5", "caption", "body"],
        "default": "body"
      }
    },
    "required": ["component", "text"]
  }
}
```

**属性说明**：

| 属性 | 类型 | 说明 |
|------|------|------|
| `text` | DynamicString | 文本内容，支持 Markdown |
| `variant` | enum | 样式变体：h1-h5, caption, body |

---

### 2. Image 组件

```json
{
  "Image": {
    "properties": {
      "component": { "const": "Image" },
      "url": { "$ref": "...DynamicString" },
      "fit": {
        "enum": ["contain", "cover", "fill", "none", "scaleDown"],
        "default": "fill"
      },
      "variant": {
        "enum": ["icon", "avatar", "smallFeature", "mediumFeature", "largeFeature", "header"],
        "default": "mediumFeature"
      }
    },
    "required": ["component", "url"]
  }
}
```

**属性说明**：

| 属性 | 类型 | 说明 |
|------|------|------|
| `url` | DynamicString | 图片 URL |
| `fit` | enum | CSS object-fit 方式 |
| `variant` | enum | 图片尺寸变体 |

---

### 3. Button 组件

```json
{
  "Button": {
    "properties": {
      "component": { "const": "Button" },
      "child": { "$ref": "...ComponentId" },
      "variant": {
        "enum": ["default", "primary", "borderless"],
        "default": "default"
      },
      "action": { "$ref": "common_types.json#/$defs/Action" }
    },
    "required": ["component", "child", "action"]
  }
}
```

**属性说明**：

| 属性 | 类型 | 说明 |
|------|------|------|
| `child` | ComponentId | 子组件 ID（通常为 Text） |
| `variant` | enum | 按钮样式：primary 主按钮 |
| `action` | Action | 点击行为 |

---

### 4. Icon 组件

```json
{
  "Icon": {
    "properties": {
      "component": { "const": "Icon" },
      "name": {
        "oneOf": [
          { "type": "string", "enum": ["accountCircle", "add", "arrowBack", ...] },
          { "type": "object", "properties": { "path": { "type": "string" } } }
        ]
      }
    },
    "required": ["component", "name"]
  }
}
```

**支持的图标** (部分)：

| 图标名 | 说明 |
|--------|------|
| `accountCircle` | 账户 |
| `add` | 添加 |
| `arrowBack` | 返回 |
| `arrowForward` | 前进 |
| `calendarToday` | 日历 |
| `call` | 电话 |
| `check` | 勾选 |
| `close` | 关闭 |
| `email` / `mail` | 邮件 |
| `favorite` | 收藏 |
| `locationOn` | 位置 |
| `person` | 个人 |
| `search` | 搜索 |
| `send` | 发送 |
| `star` | 星标 |

---

### 5. Card 组件

```json
{
  "Card": {
    "properties": {
      "component": { "const": "Card" },
      "child": { "$ref": "...ComponentId" }
    },
    "required": ["component", "child"]
  }
}
```

---

### 6. Row / Column 组件

```json
{
  "Row": {
    "properties": {
      "component": { "const": "Row" },
      "children": { "$ref": "common_types.json#/$defs/ChildList" },
      "distribution": { "enum": ["equalSpace", "center", "start", "end", "stretch"] },
      "alignment": { "enum": ["center", "start", "end", "stretch"] }
    }
  },
  "Column": {
    "properties": {
      "component": { "const": "Column" },
      "children": { "$ref": "common_types.json#/$defs/ChildList" },
      "distribution": { "enum": ["equalSpace", "center", "start", "end", "stretch"] },
      "alignment": { "enum": ["center", "start", "end", "stretch"] },
      "weight": { "type": "number" }
    }
  }
}
```

---

### 7. Form 组件

#### TextField

```json
{
  "TextField": {
    "properties": {
      "component": { "const": "TextField" },
      "value": { "$ref": "DynamicValue" },
      "placeholder": { "$ref": "DynamicString" },
      "label": { "$ref": "DynamicString" }
    }
  }
}
```

#### CheckBox

```json
{
  "CheckBox": {
    "properties": {
      "component": { "const": "CheckBox" },
      "checked": { "$ref": "DynamicBoolean" },
      "label": { "$ref": "DynamicString" }
    }
  }
}
```

#### Slider

```json
{
  "Slider": {
    "properties": {
      "component": { "const": "Slider" },
      "value": { "$ref": "DynamicNumber" },
      "min": { "type": "number" },
      "max": { "type": "number" },
      "step": { "type": "number" }
    }
  }
}
```

---

## Common Types 公共类型

### 数据绑定类型

| 类型 | 说明 |
|------|------|
| `DynamicValue` | 动态值（字面量、路径、函数调用） |
| `DynamicString` | 动态字符串 |
| `DynamicNumber` | 动态数字 |
| `DynamicBoolean` | 动态布尔值 |
| `DynamicStringList` | 动态字符串列表 |

### 结构类型

| 类型 | 说明 |
|------|------|
| `ComponentId` | 组件引用 ID |
| `ChildList` | 子组件列表 |
| `DataBinding` | 数据绑定定义 |
| `FunctionCall` | 客户端函数调用 |

### 交互类型

| 类型 | 说明 |
|------|------|
| `Action` | 交互动作（服务器事件/客户端函数） |
| `Checkable` | 可检查状态 |
| `CheckRule` | 检查规则 |

---

## DynamicValue 详解

`DynamicValue` 是 A2UI 的核心概念，支持三种值类型：

```json
{
  "DynamicValue": {
    "oneOf": [
      { "type": "string" },           // 字面量
      { "type": "number" },
      { "type": "boolean" },
      { "type": "array" },
      {
        "type": "object",
        "properties": {
          "path": { "type": "string" }  // 数据路径
        }
      },
      { "$ref": "#/$defs/FunctionCall" }  // 函数调用
    ]
  }
}
```

### 值类型示例

```json
// 字面量
"literalString": "Hello World"
"literalNumber": 42
"literalBoolean": true

// 数据路径 (从 dataModel 绑定)
"path": "/name"
"path": "/contacts/0/email"

// 函数调用
{
  "call": "formatDate",
  "args": { "date": { "path": "/createdAt" } }
}
```

---

## Action 动作详解

`Action` 定义组件的交互行为：

```json
{
  "Action": {
    "oneOf": [
      // 1. 服务器事件
      {
        "properties": {
          "event": {
            "properties": {
              "name": { "type": "string" },
              "context": { "type": "object" }
            }
          }
        },
        "required": ["event"]
      },
      // 2. 客户端函数调用
      {
        "properties": {
          "call": {
            "properties": {
              "call": { "type": "string" },
              "args": { "type": "object" }
            }
          }
        },
        "required": ["call"]
      }
    ]
  }
}
```

### 示例

```json
// 服务器事件
{
  "action": {
    "event": {
      "name": "send_email",
      "context": {
        "email": { "path": "/email" },
        "subject": { "literalString": "Hello" }
      }
    }
  }
}

// 客户端函数调用
{
  "action": {
    "call": {
      "call": "openExternalLink",
      "args": {
        "url": { "path": "/profileUrl" }
      }
    }
  }
}
```

---

## 数据模型 (Data Model)

A2UI 使用数据模型绑定 UI 组件：

```json
{
  "dataModelUpdate": {
    "surfaceId": "contact-card",
    "path": "/",
    "contents": [
      { "key": "name", "valueString": "David Chen" },
      { "key": "email", "valueString": "david@example.com" },
      { "key": "avatar", "valueString": "https://..." }
    ]
  }
}
```

### 数据类型

| 类型 | JSON Schema 类型 |
|------|------------------|
| `valueString` | string |
| `valueNumber` | number |
| `valueBoolean` | boolean |
| `valueArray` | array |
| `valueObject` | object |

---

## 渲染流程

```
┌─────────────────────────────────────────────────────────────────────┐
│                         A2UI 渲染流程                                  │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  1. beginRendering (声明渲染表面)                                     │
│     └── surfaceId + root 组件 ID                                     │
│                                                                      │
│  2. surfaceUpdate (定义 UI 组件树)                                   │
│     └── components: [                                               │
│         { "id": "card", "component": { "Card": { "child": "col" } } },│
│         { "id": "col", "component": { "Column": { ... } } },        │
│         ...                                                          │
│     ]                                                               │
│                                                                      │
│  3. dataModelUpdate (绑定数据)                                       │
│     └── contents: [                                                 │
│         { "key": "name", "valueString": "..." },                    │
│         ...                                                          │
│     ]                                                               │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 总结

| 类别 | 组件/类型 |
|------|-----------|
| **布局** | Row, Column, Card, List, Tabs, Modal |
| **内容** | Text, Image, Icon, Video, AudioPlayer, Divider |
| **交互** | Button, TextField, CheckBox, ChoicePicker, Slider, DateTimeInput |
| **数据类型** | DynamicValue, DynamicString, DynamicNumber, DynamicBoolean |
| **交互动作** | Action (event / call) |
| **数据绑定** | path (路径) / literalXXX (字面量) |
