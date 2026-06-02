# standard_catalog_definition.json

## 文件概述

A2UI 标准目录（Standard Catalog）的组件和样式定义。该文件定义了 A2UI v0.8 协议中所有标准组件的属性 Schema 和可用样式，是 A2UI 客户端渲染引擎的核心参考。当 `beginRendering` 消息中未指定 `catalogId` 时，客户端默认使用此目录。

## 数据结构

顶层包含两个主要部分：

- `components` - 组件定义映射，每个键为组件名称，值为该组件属性的 JSON Schema
- `styles` - 样式定义映射，每个键为样式名称，值为该样式的 JSON Schema

## 组件定义

### Text（文本组件）

| 属性名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| text | object | 是 | 文本内容，支持 literalString 或 path 引用数据模型 |
| usageHint | string | 否 | 文本样式提示：h1/h2/h3/h4/h5/caption/body |

### Image（图片组件）

| 属性名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| url | object | 是 | 图片 URL，支持 literalString 或 path |
| fit | string | 否 | 图片适配方式：contain/cover/fill/none/scale-down |
| usageHint | string | 否 | 图片尺寸提示：icon/avatar/smallFeature/mediumFeature/largeFeature/header |

### Icon（图标组件）

| 属性名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| name | object | 是 | 图标名称，支持 literalString（预定义枚举）或 path |

支持的图标名称：accountCircle, add, arrowBack, arrowForward, attachFile, calendarToday, call, camera, check, close, delete, download, edit, event, error, favorite, favoriteOff, folder, help, home, info, locationOn, lock, lockOpen, mail, menu, moreVert, moreHoriz, notificationsOff, notifications, payment, person, phone, photo, print, refresh, search, send, settings, share, shoppingCart, star, starHalf, starOff, upload, visibility, visibilityOff, warning

### Video（视频组件）

| 属性名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| url | object | 是 | 视频 URL，支持 literalString 或 path |

### AudioPlayer（音频播放组件）

| 属性名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| url | object | 是 | 音频 URL，支持 literalString 或 path |
| description | object | 否 | 音频描述，支持 literalString 或 path |

### Row（水平布局）

| 属性名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| children | object | 是 | 子组件定义，支持 explicitList 或 template |
| distribution | string | 否 | 主轴排列：center/end/spaceAround/spaceBetween/spaceEvenly/start |
| alignment | string | 否 | 交叉轴对齐：start/center/end/stretch |

### Column（垂直布局）

| 属性名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| children | object | 是 | 子组件定义，支持 explicitList 或 template |
| distribution | string | 否 | 主轴排列：start/center/end/spaceBetween/spaceAround/spaceEvenly |
| alignment | string | 否 | 交叉轴对齐：center/end/start/stretch |

### List（列表容器）

| 属性名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| children | object | 是 | 子组件定义，支持 explicitList 或 template |
| direction | string | 否 | 列表方向：vertical/horizontal |
| alignment | string | 否 | 交叉轴对齐：start/center/end/stretch |

### Card（卡片容器）

| 属性名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| child | string | 是 | 卡片内子组件的 ID |

### Tabs（标签页）

| 属性名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| tabItems | array | 是 | 标签项数组，每项包含 title（literalString/path）和 child（组件 ID） |

### Divider（分隔线）

| 属性名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| axis | string | 否 | 分隔线方向：horizontal/vertical |

### Modal（模态框）

| 属性名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| entryPointChild | string | 是 | 触发模态框的组件 ID |
| contentChild | string | 是 | 模态框内容组件 ID |

### Button（按钮）

| 属性名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| child | string | 是 | 按钮内显示的组件 ID |
| primary | boolean | 否 | 是否为主要操作按钮 |
| action | object | 是 | 点击时派发的动作，包含 name 和可选 context |

### CheckBox（复选框）

| 属性名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| label | object | 是 | 复选框标签，支持 literalString 或 path |
| value | object | 是 | 选中状态，支持 literalBoolean 或 path |

### TextField（文本输入）

| 属性名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| label | object | 是 | 输入框标签，支持 literalString 或 path |
| text | object | 否 | 输入框值，支持 literalString 或 path |
| textFieldType | string | 否 | 输入类型：date/longText/number/shortText/obscured |
| validationRegexp | string | 否 | 客户端验证正则表达式 |

### DateTimeInput（日期时间输入）

| 属性名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| value | object | 是 | ISO 8601 格式的日期/时间值，支持 literalString 或 path |
| enableDate | boolean | 否 | 是否允许选择日期 |
| enableTime | boolean | 否 | 是否允许选择时间 |

### MultipleChoice（多选组件）

| 属性名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| selections | object | 是 | 当前选中值，支持 literalArray 或 path |
| options | array | 是 | 可选项数组，每项包含 label 和 value |
| maxAllowedSelections | integer | 否 | 最大可选数量 |
| variant | string | 否 | 显示样式：checkbox/chips |
| filterable | boolean | 否 | 是否显示搜索过滤输入框 |

### Slider（滑块）

| 属性名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| value | object | 是 | 当前值，支持 literalNumber 或 path |
| minValue | number | 否 | 最小值 |
| maxValue | number | 否 | 最大值 |

## 样式定义

| 样式名 | 类型 | 说明 |
|--------|------|------|
| font | string | UI 主字体 |
| primaryColor | string | 主色调，十六进制格式（如 #00BFFF），匹配 ^#[0-9a-fA-F]{6}$ |

## 示例

```json
{
  "components": {
    "Text": {
      "type": "object",
      "properties": {
        "text": { "type": "object", "properties": { "literalString": { "type": "string" }, "path": { "type": "string" } } },
        "usageHint": { "type": "string", "enum": ["h1", "h2", "h3", "h4", "h5", "caption", "body"] }
      },
      "required": ["text"]
    }
  },
  "styles": {
    "font": { "type": "string" },
    "primaryColor": { "type": "string", "pattern": "^#[0-9a-fA-F]{6}$" }
  }
}
```
