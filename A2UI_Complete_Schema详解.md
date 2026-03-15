# A2UI 完整消息Schema (v0.8) - 集成标准组件目录

> 本文件解读 `/specification/v0_8/json/server_to_client_with_standard_catalog.json`

## 概述

这是 A2UI v0.8 版本的**完整消息Schema**，将消息操作与标准组件目录集成在一起。该Schema定义了：

1. **四种核心UI操作**
2. **18种标准UI组件**
3. **样式定义**
4. **数据绑定机制**

***

## 1. 核心操作 (Action Properties)

每条A2UI消息**必须包含且仅包含**以下四种操作之一：

| 操作                | 作用           | 必填字段                      |
| ----------------- | ------------ | ------------------------- |
| `beginRendering`  | 开始渲染一个新的UI表面 | `surfaceId`, `root`       |
| `surfaceUpdate`   | 更新UI表面的组件列表  | `surfaceId`, `components` |
| `dataModelUpdate` | 更新UI表面的数据模型  | `surfaceId`, `contents`   |
| `deleteSurface`   | 删除指定的UI表面    | `surfaceId`               |

***

## 2. beginRendering - 开始渲染

通知客户端开始渲染一个新的UI表面。

### 字段定义

| 字段          | 类型     |  必填 | 说明         |
| ----------- | ------ | :-: | ---------- |
| `surfaceId` | string |  ✅  | UI表面的唯一标识符 |
| `root`      | string |  ✅  | 根组件的ID     |
| `styles`    | object |  ❌  | 样式信息       |

### styles 子字段

| 字段             | 类型     | 说明                    |
| -------------- | ------ | --------------------- |
| `font`         | string | 主要字体                  |
| `primaryColor` | string | 主色调（十六进制，如 `#00BFFF`） |

### 示例

```json
{
  "beginRendering": {
    "surfaceId": "main_surface",
    "root": "root_component",
    "styles": {·
      "font": "Roboto",
      "primaryColor": "#2196F3"
    }
  }
}
```

***

## 3. surfaceUpdate - 更新表面

用新的组件列表更新指定的UI表面。

### 字段定义

| 字段           | 类型     |  必填 | 说明         |
| ------------ | ------ | :-: | ---------- |
| `surfaceId`  | string |  ✅  | UI表面的唯一标识符 |
| `components` | array  |  ✅  | 组件列表（至少1个） |

### 组件结构

| 字段          | 类型     |  必填 | 说明                  |
| ----------- | ------ | :-: | ------------------- |
| `id`        | string |  ✅  | 组件唯一ID              |
| `weight`    | number |  ❌  | 相对权重（CSS flex-grow） |
| `component` | object |  ✅  | 组件内容                |

***

## 4. 标准组件目录 (Standard Components)

### 4.1 展示组件

#### Text - 文本

```json
{
  "Text": {
    "text": { "literalString": "标题文本" },
    "usageHint": "h1"
  }
}
```

**usageHint可选值**: `h1`, `h2`, `h3`, `h4`, `h5`, `caption`, `body`

***

#### Image - 图片

```json
{
  "Image": {
    "url": { "literalString": "https://example.com/image.jpg" },
    "fit": "cover",
    "usageHint": "mediumFeature"
  }
}
```

**fit可选值**: `contain`, `cover`, `fill`, `none`, `scale-down`\
**usageHint可选值**: `icon`, `avatar`, `smallFeature`, `mediumFeature`, `largeFeature`, `header`

***

#### Icon - 图标

```json
{
  "Icon": {
    "name": { "literalString": "star" }
  }
}
```

**可用图标**: `accountCircle`, `add`, `arrowBack`, `arrowForward`, `check`, `close`, `delete`, `download`, `edit`, `favorite`, `help`, `home`, `info`, `locationOn`, `lock`, `mail`, `menu`, `notifications`, `person`, `phone`, `search`, `send`, `settings`, `share`, `shoppingCart`, `star`, `warning` 等

***

#### Video - 视频

```json
{
  "Video": {
    "url": { "literalString": "https://example.com/video.mp4" }
  }
}
```

***

#### AudioPlayer - 音频播放器

```json
{
  "AudioPlayer": {
    "url": { "literalString": "https://example.com/audio.mp3" },
    "description": { "literalString": "轻音乐" }
  }
}
```

***

#### Divider - 分割线

```json
{
  "Divider": {
    "axis": "horizontal"
  }
}
```

**axis可选值**: `horizontal`, `vertical`

***

### 4.2 布局组件

#### Row - 行布局

```json
{
  "Row": {
    "children": {
      "explicitList": ["child1", "child2"]
    },
    "distribution": "spaceBetween",
    "alignment": "center"
  }
}
```

**distribution**: `center`, `end`, `spaceAround`, `spaceBetween`, `spaceEvenly`, `start`\
**alignment**: `start`, `center`, `end`, `stretch`

***

#### Column - 列布局

```json
{
  "Column": {
    "children": {
      "explicitList": ["header", "content", "footer"]
    },
    "distribution": "spaceBetween",
    "alignment": "start"
  }
}
```

***

#### List - 列表

```json
{
  "List": {
    "children": {
      "explicitList": ["item1", "item2"]
    },
    "direction": "vertical",
    "alignment": "start"
  }
}
```

**direction**: `vertical`, `horizontal`\
**alignment**: `start`, `center`, `end`, `stretch`

***

#### Card - 卡片

```json
{
  "Card": {
    "child": "card_content"
  }
}
```

***

#### Tabs - 标签页

```json
{
  "Tabs": {
    "tabItems": [
      {
        "title": { "literalString": "标签1" },
        "child": "content1"
      },
      {
        "title": { "literalString": "标签2" },
        "child": "content2"
      }
    ]
  }
}
```

***

#### Modal - 模态框

```json
{
  "Modal": {
    "entryPointChild": "open_button",
    "contentChild": "modal_content"
  }
}
```

***

### 4.3 交互组件

#### Button - 按钮

```json
{
  "Button": {
    "child": "button_text",
    "primary": true,
    "action": {
      "name": "submit_form",
      "context": [
        {
          "key": "formId",
          "value": { "literalString": "booking_form" }
        }
      ]
    }
  }
}
```

***

#### CheckBox - 复选框

```json
{
  "CheckBox": {
    "label": { "literalString": "我同意" },
    "value": { "literalBoolean": false }
  }
}
```

***

#### TextField - 文本输入框

```json
{
  "TextField": {
    "label": { "literalString": "姓名" },
    "text": { "path": "/user/name" },
    "textFieldType": "shortText",
    "validationRegexp": "^[\\u4e00-\\u9fa5a-zA-Z]+$"
  }
}
```

**textFieldType**: `date`, `longText`, `number`, `shortText`, `obscured`

***

#### DateTimeInput - 日期时间输入

```json
{
  "DateTimeInput": {
    "value": { "literalString": "2025-03-15T18:30:00Z" },
    "enableDate": true,
    "enableTime": true
  }
}
```

***

#### MultipleChoice - 多选项

```json
{
  "MultipleChoice": {
    "selections": { "literalArray": ["option1"] },
    "options": [
      {
        "label": { "literalString": "川菜" },
        "value": "sichuan"
      },
      {
        "label": { "literalString": "粤菜" },
        "value": "cantonese"
      }
    ],
    "maxAllowedSelections": 2,
    "variant": "chips",
    "filterable": true
  }
}
```

**variant**: `checkbox`, `chips`

***

#### Slider - 滑块

```json
{
  "Slider": {
    "value": { "literalNumber": 3 },
    "minValue": 1,
    "maxValue": 5
  }
}
```

***

## 5. dataModelUpdate - 更新数据模型

### 字段定义

| 字段          | 类型     |  必填 | 说明                   |
| ----------- | ------ | :-: | -------------------- |
| `surfaceId` | string |  ✅  | UI表面ID               |
| `path`      | string |  ❌  | 数据路径（如 `/user/name`） |
| `contents`  | array  |  ✅  | 数据条目数组               |

### 数据条目

| 字段             | 类型      |   必填   | 说明   |
| -------------- | ------- | :----: | ---- |
| `key`          | string  |    ✅   | 数据键名 |
| `valueString`  | string  | <br /> | 字符串值 |
| `valueNumber`  | number  | <br /> | 数值   |
| `valueBoolean` | boolean | <br /> | 布尔值  |
| `valueMap`     | array   | <br /> | 映射表  |

### 示例

```json
{
  "dataModelUpdate": {
    "surfaceId": "main_surface",
    "path": "/",
    "contents": [
      { "key": "username", "valueString": "张三" },
      { "key": "age", "valueNumber": 28 },
      { "key": "isActive", "valueBoolean": true },
      { "key": "items", "valueMap": [
        { "key": "item1", "valueString": "苹果" },
        { "key": "item2", "valueString": "香蕉" }
      ]}
    ]
  }
}
```

***

## 6. deleteSurface - 删除表面

```json
{
  "deleteSurface": {
    "surfaceId": "main_surface"
  }
}
```

***

## 7. 数据绑定机制

A2UI支持两种方式定义值：

### 7.1 字面值 (Literal)

```json
{ "literalString": "Hello" }
{ "literalNumber": 42 }
{ "literalBoolean": true }
{ "literalArray": ["a", "b", "c"] }
```

### 7.2 数据模型路径 (Path)

```json
{ "path": "/user/name" }
{ "path": "/form/email" }
{ "path": "/list/items" }
```

***

## 8. 完整示例

```json
{
  "beginRendering": {
    "surfaceId": "restaurant_booking",
    "root": "root_layout",
    "styles": {
      "font": "Roboto",
      "primaryColor": "#FF5722"
    }
  },
  "surfaceUpdate": {
    "surfaceId": "restaurant_booking",
    "components": [
      {
        "id": "root_layout",
        "component": {
          "Column": {
            "children": ["header", "form_container", "submit_button"]
          }
        }
      },
      {
        "id": "header",
        "component": {
          "Text": {
            "text": { "literalString": "餐厅预订" },
            "usageHint": "h1"
          }
        }
      },
      {
        "id": "form_container",
        "component": {
          "Column": {
            "children": ["name_field", "date_field", "guests_field"]
          }
        }
      },
      {
        "id": "name_field",
        "component": {
          "TextField": {
            "label": { "literalString": "姓名" },
            "textFieldType": "shortText"
          }
        }
      },
      {
        "id": "date_field",
        "component": {
          "DateTimeInput": {
            "value": { "path": "/booking/date" },
            "enableDate": true,
            "enableTime": true
          }
        }
      },
      {
        "id": "guests_field",
        "component": {
          "Slider": {
            "value": { "literalNumber": 2 },
            "minValue": 1,
            "maxValue": 10
          }
        }
      },
      {
        "id": "submit_button",
        "component": {
          "Button": {
            "child": "button_label",
            "primary": true,
            "action": {
              "name": "submit_booking"
            }
          }
        }
      },
      {
        "id": "button_label",
        "component": {
          "Text": {
            "text": { "literalString": "提交预订" }
          }
        }
      }
    ]
  },
  "dataModelUpdate": {
    "surfaceId": "restaurant_booking",
    "contents": [
      { "key": "booking_date", "valueString": "" },
      { "key": "guests_count", "valueNumber": 2 }
    ]
  }
}
```

***

## 9. Schema结构图

```
server_to_client_with_standard_catalog.json
├── beginRendering
│   ├── surfaceId (required)
│   ├── root (required)
│   └── styles
│       ├── font
│       └── primaryColor
├── surfaceUpdate
│   ├── surfaceId (required)
│   └── components[]
│       ├── id (required)
│       ├── weight
│       └── component (required)
│           ├── Text
│           ├── Image
│           ├── Icon
│           ├── Video
│           ├── AudioPlayer
│           ├── Row
│           ├── Column
│           ├── List
│           ├── Card
│           ├── Tabs
│           ├── Divider
│           ├── Modal
│           ├── Button
│           ├── CheckBox
│           ├── TextField
│           ├── DateTimeInput
│           ├── MultipleChoice
│           └── Slider
├── dataModelUpdate
│   ├── surfaceId (required)
│   ├── path
│   └── contents[]
│       ├── key (required)
│       ├── valueString
│       ├── valueNumber
│       ├── valueBoolean
│       └── valueMap
└── deleteSurface
    └── surfaceId (required)
```

