# A2UI 标准组件目录定义 (v0.8)

> 本文件解读 `/specification/v0_8/json/standard_catalog_definition.json` 中的标准组件定义

## 概述

`standard_catalog_definition.json` 定义了 A2UI v0.8 版本中所有可用的标准UI组件。这些组件是 A2UI 代理可以请求渲染的预定义组件库，确保了安全性和一致性。

---

## 组件分类

| 类别 | 组件 |
|------|------|
| **展示组件** | Text, Image, Icon, Video, AudioPlayer, Divider |
| **布局组件** | Row, Column, List, Card, Tabs, Modal |
| **交互组件** | Button, CheckBox, TextField, DateTimeInput, MultipleChoice, Slider |

---

## 1. 展示组件

### 1.1 Text - 文本

用于显示文本内容，支持简单的Markdown格式化。

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `text` | object | ✅ | 文本内容 |
| `text.literalString` | string | | 直接的字符串文本 |
| `text.path` | string | | 数据模型路径（如 `/doc/title`） |
| `usageHint` | string | ❌ | 文本样式提示 |

**usageHint 可选值**：
- `h1` - 最大标题
- `h2` - 第二大标题
- `h3` - 第三大标题
- `h4` - 第四大标题
- `h5` - 第五大标题
- `caption` - caption文本
- `body` - 标准正文文本

**示例**：
```json
{
  "text": {
    "literalString": "欢迎来到餐厅推荐"
  },
  "usageHint": "h1"
}
```

---

### 1.2 Image - 图片

用于显示图片。

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `url` | object | ✅ | 图片URL |
| `url.literalString` | string | | 直接的URL字符串 |
| `url.path` | string | | 数据模型路径 |
| `fit` | string | ❌ | 图片适应方式 |
| `usageHint` | string | ❌ | 图片样式提示 |

**fit 可选值**：
- `contain` - 包含
- `cover` - 覆盖
- `fill` - 填充
- `none` - 无
- `scale-down` - 缩小

**usageHint 可选值**：
- `icon` - 小方形图标
- `avatar` - 圆形头像
- `smallFeature` - 小特性图
- `mediumFeature` - 中特性图
- `largeFeature` - 大特性图
- `header` - 全宽标题图

**示例**：
```json
{
  "url": {
    "literalString": "https://example.com/image.jpg"
  },
  "fit": "cover",
  "usageHint": "mediumFeature"
}
```

---

### 1.3 Icon - 图标

用于显示Material Design图标。

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `name` | object | ✅ | 图标名称 |
| `name.literalString` | string | | 图标名称枚举 |
| `name.path` | string | | 数据模型路径 |

**可用图标名称**：
```
accountCircle, add, arrowBack, arrowForward, attachFile, calendarToday,
call, camera, check, close, delete, download, edit, event, error,
favorite, favoriteOff, folder, help, home, info, locationOn, lock,
lockOpen, mail, menu, moreVert, moreHoriz, notificationsOff,
notifications, payment, person, phone, photo, print, refresh,
search, send, settings, share, shoppingCart, star, starHalf,
starOff, upload, visibility, visibilityOff, warning
```

**示例**：
```json
{
  "name": {
    "literalString": "star"
  }
}
```

---

### 1.4 Video - 视频

用于显示视频内容。

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `url` | object | ✅ | 视频URL |
| `url.literalString` | string | | 直接的URL字符串 |
| `url.path` | string | | 数据模型路径 |

**示例**：
```json
{
  "url": {
    "literalString": "https://example.com/video.mp4"
  }
}
```

---

### 1.5 AudioPlayer - 音频播放器

用于播放音频内容。

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `url` | object | ✅ | 音频URL |
| `description` | object | ❌ | 音频描述（标题/摘要） |

**示例**：
```json
{
  "url": {
    "literalString": "https://example.com/audio.mp3"
  },
  "description": {
    "literalString": "轻音乐 - 放松"
  }
}
```

---

### 1.6 Divider - 分割线

用于分隔内容的水平或垂直线条。

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `axis` | string | ❌ | 分割线方向 |

**axis 可选值**：
- `horizontal` - 水平
- `vertical` - 垂直

**示例**：
```json
{
  "axis": "horizontal"
}
```

---

## 2. 布局组件

### 2.1 Row - 行布局

水平排列子组件，类似于CSS Flexbox的Row。

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `children` | object | ✅ | 子组件定义 |
| `children.explicitList` | array | | 固定子组件列表 |
| `children.template` | object | | 动态模板 |
| `distribution` | string | ❌ | 主轴排列方式 |
| `alignment` | string | ❌ | 交叉轴对齐方式 |

**distribution 可选值**（对应CSS justify-content）：
- `center` - 居中
- `end` - 末尾
- `spaceAround` - 周围间距
- `spaceBetween` - 之间间距
- `spaceEvenly` - 等间距
- `start` - 起始

**alignment 可选值**（对应CSS align-items）：
- `start` - 起始对齐
- `center` - 居中对齐
- `end` - 末尾对齐
- `stretch` - 拉伸

**示例**：
```json
{
  "children": {
    "explicitList": ["icon_1", "text_1", "button_1"]
  },
  "distribution": "spaceBetween",
  "alignment": "center"
}
```

---

### 2.2 Column - 列布局

垂直排列子组件，类似于CSS Flexbox的Column。

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `children` | object | ✅ | 子组件定义 |
| `children.explicitList` | array | | 固定子组件列表 |
| `children.template` | object | | 动态模板 |
| `distribution` | string | ❌ | 主轴排列方式 |
| `alignment` | string | ❌ | 交叉轴对齐方式 |

**distribution 可选值**：
- `start`, `center`, `end`, `spaceBetween`, `spaceAround`, `spaceEvenly`

**alignment 可选值**：
- `center`, `end`, `start`, `stretch`

**示例**：
```json
{
  "children": {
    "explicitList": ["header", "content", "footer"]
  },
  "distribution": "spaceBetween",
  "alignment": "start"
}
```

---

### 2.3 List - 列表

以列表形式展示子组件。

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `children` | object | ✅ | 子组件定义 |
| `direction` | string | ❌ | 列表方向 |
| `alignment` | string | ❌ | 对齐方式 |

**direction 可选值**：
- `vertical` - 垂直
- `horizontal` - 水平

**alignment 可选值**：
- `start`, `center`, `end`, `stretch`

**示例**：
```json
{
  "children": {
    "template": {
      "componentId": "list_item_template",
      "dataBinding": "/items"
    }
  },
  "direction": "vertical",
  "alignment": "start"
}
```

---

### 2.4 Card - 卡片

用于包装单个子组件的卡片容器。

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `child` | string | ✅ | 卡片内显示的组件ID |

**示例**：
```json
{
  "child": "card_content"
}
```

---

### 2.5 Tabs - 标签页

用于显示多个标签页，每个标签页包含不同的内容。

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `tabItems` | array | ✅ | 标签页数组 |

**tabItems 数组项**：

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `title` | object | ✅ | 标签标题 |
| `child` | string | ✅ | 标签页内容组件ID |

**示例**：
```json
{
  "tabItems": [
    {
      "title": {
        "literalString": "餐厅列表"
      },
      "child": "restaurant_list_content"
    },
    {
      "title": {
        "literalString": "地图"
      },
      "child": "map_content"
    }
  ]
}
```

---

### 2.6 Modal - 模态框

用于显示模态对话框。

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `entryPointChild` | string | ✅ | 打开模态框的组件ID（如按钮） |
| `contentChild` | string | ✅ | 模态框内容组件ID |

**示例**：
```json
{
  "entryPointChild": "open_modal_button",
  "contentChild": "modal_content"
}
```

---

## 3. 交互组件

### 3.1 Button - 按钮

用于触发客户端操作的按钮。

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `child` | string | ✅ | 按钮内显示的组件ID（通常是Text） |
| `primary` | boolean | ❌ | 是否为主要操作按钮 |
| `action` | object | ✅ | 点击按钮时的操作 |

**action 结构**：

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `name` | string | ✅ | 操作名称 |
| `context` | array | ❌ | 操作上下文数据 |

**示例**：
```json
{
  "child": "button_text",
  "primary": true,
  "action": {
    "name": "submit_form",
    "context": [
      {
        "key": "formId",
        "value": {
          "literalString": "booking_form"
        }
      }
    ]
  }
}
```

---

### 3.2 CheckBox - 复选框

用于二进制选择的复选框。

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `label` | object | ✅ | 复选框标签 |
| `value` | object | ✅ | 当前选中状态 |

**示例**：
```json
{
  "label": {
    "literalString": "我同意服务条款"
  },
  "value": {
    "literalBoolean": false
  }
}
```

---

### 3.3 TextField - 文本输入框

用于用户输入文本。

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `label` | object | ✅ | 输入框标签 |
| `text` | object | ❌ | 当前文本值 |
| `textFieldType` | string | ❌ | 输入框类型 |
| `validationRegexp` | string | ❌ | 客户端验证正则表达式 |

**textFieldType 可选值**：
- `date` - 日期
- `longText` - 长文本
- `number` - 数字
- `shortText` - 短文本
- `obscured` - 隐藏文本（密码）

**示例**：
```json
{
  "label": {
    "literalString": "姓名"
  },
  "text": {
    "path": "/user/name"
  },
  "textFieldType": "shortText",
  "validationRegexp": "^[\\u4e00-\\u9fa5a-zA-Z]+$"
}
```

---

### 3.4 DateTimeInput - 日期时间输入

用于选择日期和/或时间。

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `value` | object | ✅ | 当前选择的日期/时间值（ISO 8601格式） |
| `enableDate` | boolean | ❌ | 是否允许选择日期 |
| `enableTime` | boolean | ❌ | 是否允许选择时间 |

**示例**：
```json
{
  "value": {
    "literalString": "2025-03-15T18:30:00Z"
  },
  "enableDate": true,
  "enableTime": true
}
```

---

### 3.5 MultipleChoice - 多选项

用于从多个选项中选择一个或多个。

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `selections` | object | ✅ | 当前选中的选项 |
| `options` | array | ✅ | 可用选项数组 |
| `maxAllowedSelections` | integer | ❌ | 最大允许选择数量 |
| `variant` | string | ❌ | 显示样式 |
| `filterable` | boolean | ❌ | 是否可过滤 |

**variant 可选值**：
- `checkbox` - 复选框样式
- `chips` - 标签样式

**示例**：
```json
{
  "selections": {
    "literalArray": ["option1"]
  },
  "options": [
    {
      "label": {
        "literalString": "川菜"
      },
      "value": "sichuan"
    },
    {
      "label": {
        "literalString": "粤菜"
      },
      "value": "cantonese"
    }
  ],
  "maxAllowedSelections": 2,
  "variant": "chips",
  "filterable": true
}
```

---

### 3.6 Slider - 滑块

用于选择一个范围内的数值。

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `value` | object | ✅ | 当前值 |
| `minValue` | number | ❌ | 最小值 |
| `maxValue` | number | ❌ | 最大值 |

**示例**：
```json
{
  "value": {
    "literalNumber": 3
  },
  "minValue": 1,
  "maxValue": 5
}
```

---

## 4. 样式定义

### 全局样式

| 字段 | 类型 | 说明 |
|------|------|------|
| `font` | string | 主要字体 |
| `primaryColor` | string | 主色调（十六进制，如 `#00BFFF`） |

**示例**：
```json
{
  "font": "Roboto",
  "primaryColor": "#2196F3"
}
```

---

## 5. 数据绑定机制

A2UI的一个重要特性是支持数据绑定。大多数组件属性可以是：

1. **字面值**：直接指定的值
   ```json
   { "literalString": "Hello" }
   ```

2. **数据模型路径**：引用数据模型中的值
   ```json
   { "path": "/user/name" }
   ```

这种设计使得UI可以动态更新，而无需重新定义整个组件树。

---

## 完整示例

```json
{
  "beginRendering": {
    "surfaceId": "booking_surface",
    "root": "root_layout"
  },
  "surfaceUpdate": {
    "surfaceId": "booking_surface",
    "components": [
      {
        "id": "root_layout",
        "component": {
          "column": {
            "children": ["header", "form", "submit_button"]
          }
        }
      },
      {
        "id": "header",
        "component": {
          "text": {
            "literalString": "餐厅预订"
          },
          "usageHint": "h1"
        }
      },
      {
        "id": "form",
        "component": {
          "column": {
            "children": ["name_field", "date_field", "guests_field"]
          }
        }
      },
      {
        "id": "name_field",
        "component": {
          "textField": {
            "label": { "literalString": "姓名" },
            "textFieldType": "shortText"
          }
        }
      },
      {
        "id": "date_field",
        "component": {
          "dateTimeInput": {
            "value": { "path": "/booking/date" },
            "enableDate": true,
            "enableTime": true
          }
        }
      },
      {
        "id": "guests_field",
        "component": {
          "slider": {
            "value": { "literalNumber": 2 },
            "minValue": 1,
            "maxValue": 10
          }
        }
      },
      {
        "id": "submit_button",
        "component": {
          "button": {
            "child": "button_text",
            "primary": true,
            "action": {
              "name": "submit_booking"
            }
          }
        }
      },
      {
        "id": "button_text",
        "component": {
          "text": {
            "literalString": "提交预订"
          }
        }
      }
    ]
  }
}
```
