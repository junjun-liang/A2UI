# A2UI v0.10 基础组件目录详解

> 本文件详解 `/specification/v0_10/json/basic_catalog.json`

---

## 概述

`basic_catalog.json` 是 A2UI v0.10 的核心定义文件，包含了：
- **18种基础UI组件**
- **14种内置函数**
- **主题系统定义**
- **通用组件属性**

文件结构：
```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "catalogId": "https://a2ui.org/specification/v0_10/basic_catalog.json",
  "components": { ... },
  "functions": { ... },
  "$defs": { ... }
}
```

---

## 一、组件 (Components)

### 1.1 展示组件 (Display Components)

#### Text - 文本

```json
{
  "component": "Text",
  "text": { "literalString": "标题文本" },
  "variant": "h1"
}
```

| 属性 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `component` | const | ✅ | 固定值 "Text" |
| `text` | DynamicString | ✅ | 文本内容 |
| `variant` | enum | ❌ | 文本样式 |

**variant 可选值**：
| 值 | 说明 |
|-----|------|
| `h1` | 一级标题（最大） |
| `h2` | 二级标题 |
| `h3` | 三级标题 |
| `h4` | 四级标题 |
| `h5` | 五级标题 |
| `caption` | caption文本 |
| `body` | 正文（默认） |

---

#### Image - 图片

```json
{
  "component": "Image",
  "url": { "literalString": "https://example.com/image.jpg" },
  "fit": "cover",
  "variant": "mediumFeature"
}
```

| 属性 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `component` | const | ✅ | 固定值 "Image" |
| `url` | DynamicString | ✅ | 图片URL |
| `fit` | enum | ❌ | 适应方式 |
| `variant` | enum | ❌ | 图片样式提示 |

**fit 可选值**：
| 值 | 说明 | 默认 |
|-----|------|-----|
| `contain` | 包含 | |
| `cover` | 覆盖 | ✅ |
| `fill` | 填充 | |
| `none` | 无 | |
| `scaleDown` | 缩小 | |

**variant 可选值**：
| 值 | 说明 | 默认 |
|-----|------|-----|
| `icon` | 小方形图标 | |
| `avatar` | 圆形头像 | |
| `smallFeature` | 小特性图 | |
| `mediumFeature` | 中特性图 | ✅ |
| `largeFeature` | 大特性图 | |
| `header` | 全宽标题图 | |

---

#### Icon - 图标

```json
{
  "component": "Icon",
  "name": { "literalString": "star" }
}
```

| 属性 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `component` | const | ✅ | 固定值 "Icon" |
| `name` | string/object | ✅ | 图标名称或数据绑定 |

**可用图标列表**（60+图标）：
```
accountCircle, add, arrowBack, arrowForward, attachFile, calendarToday,
call, camera, check, close, delete, download, edit, event,
fastForward, favorite, favoriteOff, folder, help, home, info,
locationOn, lock, lockOpen, mail, menu, moreVert, moreHoriz,
notificationsOff, notifications, pause, payment, person, phone,
photo, play, print, refresh, rewind, search, send, settings,
share, shoppingCart, skipNext, skipPrevious, star, starHalf,
starOff, stop, upload, visibility, visibilityOff, volumeDown,
volumeMute, volumeOff, volumeUp, warning
```

---

#### Video - 视频

```json
{
  "component": "Video",
  "url": { "literalString": "https://example.com/video.mp4" }
}
```

| 属性 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `component` | const | ✅ | 固定值 "Video" |
| `url` | DynamicString | ✅ | 视频URL |

---

#### AudioPlayer - 音频播放器

```json
{
  "component": "AudioPlayer",
  "url": { "literalString": "https://example.com/audio.mp3" },
  "description": { "literalString": "轻音乐 - 放松" }
}
```

| 属性 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `component` | const | ✅ | 固定值 "AudioPlayer" |
| `url` | DynamicString | ✅ | 音频URL |
| `description` | DynamicString | ❌ | 音频描述 |

---

#### Divider - 分割线

```json
{
  "component": "Divider",
  "axis": "horizontal"
}
```

| 属性 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `component` | const | ✅ | 固定值 "Divider" |
| `axis` | enum | ❌ | 方向 |

**axis 可选值**：
| 值 | 说明 | 默认 |
|-----|------|-----|
| `horizontal` | 水平 | ✅ |
| `vertical` | 垂直 | |

---

### 1.2 布局组件 (Layout Components)

#### Row - 行布局

```json
{
  "component": "Row",
  "children": ["icon", "title", "action"],
  "justify": "spaceBetween",
  "align": "center"
}
```

| 属性 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `component` | const | ✅ | 固定值 "Row" |
| `children` | ChildList | ✅ | 子组件列表 |
| `justify` | enum | ❌ | 主轴排列方式 |
| `align` | enum | ❌ | 交叉轴对齐方式 |

**justify (水平方向) 可选值**：
| 值 | 说明 | 默认 |
|-----|------|-----|
| `center` | 居中 | |
| `end` | 末尾 | |
| `spaceAround` | 周围间距 | |
| `spaceBetween` | 之间间距 | |
| `spaceEvenly` | 等间距 | |
| `start` | 起始 | ✅ |
| `stretch` | 拉伸 | |

**align (垂直方向) 可选值**：
| 值 | 说明 | 默认 |
|-----|------|-----|
| `start` | 起始 | ✅ |
| `center` | 居中 | |
| `end` | 末尾 | |
| `stretch` | 拉伸 | |

---

#### Column - 列布局

```json
{
  "component": "Column",
  "children": ["header", "content", "footer"],
  "justify": "spaceBetween",
  "align": "start"
}
```

| 属性 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `component` | const | ✅ | 固定值 "Column" |
| `children` | ChildList | ✅ | 子组件列表 |
| `justify` | enum | ❌ | 主轴排列方式 |
| `align` | enum | ❌ | 交叉轴对齐方式 |

**justify (垂直方向) 可选值**：
| 值 | 说明 | 默认 |
|-----|------|-----|
| `start` | 起始 | ✅ |
| `center` | 居中 | |
| `end` | 末尾 | |
| `spaceBetween` | 之间间距 | |
| `spaceAround` | 周围间距 | |
| `spaceEvenly` | 等间距 | |
| `stretch` | 拉伸 | |

---

#### List - 列表

```json
{
  "component": "List",
  "children": {
    "template": {
      "componentId": "item_template",
      "dataBinding": "/items"
    }
  },
  "direction": "vertical",
  "align": "start"
}
```

| 属性 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `component` | const | ✅ | 固定值 "List" |
| `children` | ChildList | ✅ | 子组件列表 |
| `direction` | enum | ❌ | 列表方向 |
| `align` | enum | ❌ | 对齐方式 |

**direction 可选值**：
| 值 | 说明 | 默认 |
|-----|------|-----|
| `vertical` | 垂直 | ✅ |
| `horizontal` | 水平 | |

---

#### Card - 卡片

```json
{
  "component": "Card",
  "child": "card_content"
}
```

| 属性 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `component` | const | ✅ | 固定值 "Card" |
| `child` | ComponentId | ✅ | 子组件ID |

**注意**：`child` 必须是单个组件ID，如需多个子组件，请使用 Row/Column 包装。

---

#### Tabs - 标签页

```json
{
  "component": "Tabs",
  "tabs": [
    {
      "title": { "literalString": "标签1" },
      "child": "tab1_content"
    },
    {
      "title": { "literalString": "标签2" },
      "child": "tab2_content"
    }
  ]
}
```

| 属性 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `component` | const | ✅ | 固定值 "Tabs" |
| `tabs` | array | ✅ | 标签页数组 |

**tabs 数组项**：
| 属性 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `title` | DynamicString | ✅ | 标签标题 |
| `child` | ComponentId | ✅ | 内容组件ID |

---

#### Modal - 模态框

```json
{
  "component": "Modal",
  "trigger": "open_button",
  "content": "modal_content"
}
```

| 属性 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `component` | const | ✅ | 固定值 "Modal" |
| `trigger` | ComponentId | ✅ | 触发组件ID（按钮） |
| `content` | ComponentId | ✅ | 内容组件ID |

---

### 1.3 交互组件 (Interactive Components)

#### Button - 按钮

```json
{
  "component": "Button",
  "child": "button_text",
  "variant": "primary",
  "action": {
    "event": {
      "name": "submit_form",
      "context": { "formId": "contact" }
    }
  }
}
```

| 属性 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `component` | const | ✅ | 固定值 "Button" |
| `child` | ComponentId | ✅ | 按钮内容组件ID |
| `variant` | enum | ❌ | 按钮样式 |
| `action` | Action | ✅ | 点击动作 |

**variant 可选值**：
| 值 | 说明 | 默认 |
|-----|------|-----|
| `default` | 默认样式 | ✅ |
| `primary` | 主要操作（高亮） | |
| `borderless` | 无边框（链接样式） | |

---

#### TextField - 文本输入框

```json
{
  "component": "TextField",
  "label": { "literalString": "姓名" },
  "value": { "path": "/form/name" },
  "variant": "shortText"
}
```

| 属性 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `component` | const | ✅ | 固定值 "TextField" |
| `label` | DynamicString | ✅ | 输入框标签 |
| `value` | DynamicString | ❌ | 当前值 |
| `variant` | enum | ❌ | 输入框类型 |

**variant 可选值**：
| 值 | 说明 | 默认 |
|-----|------|-----|
| `longText` | 多行文本 | |
| `number` | 数字 | |
| `shortText` | 单行文本 | ✅ |
| `obscured` | 隐藏（密码） | |

---

#### CheckBox - 复选框

```json
{
  "component": "CheckBox",
  "label": { "literalString": "我同意服务条款" },
  "value": { "path": "/form/agree" }
}
```

| 属性 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `component` | const | ✅ | 固定值 "CheckBox" |
| `label` | DynamicString | ✅ | 复选框标签 |
| `value` | DynamicBoolean | ✅ | 当前选中状态 |

---

#### ChoicePicker - 选择器

```json
{
  "component": "ChoicePicker",
  "label": { "literalString": "选择菜系" },
  "options": [
    { "label": { "literalString": "川菜" }, "value": "sichuan" },
    { "label": { "literalString": "粤菜" }, "value": "cantonese" }
  ],
  "value": { "literalArray": ["sichuan"] },
  "variant": "multipleSelection",
  "displayStyle": "chips",
  "filterable": true
}
```

| 属性 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `component` | const | ✅ | 固定值 "ChoicePicker" |
| `label` | DynamicString | ✅ | 选择器标签 |
| `options` | array | ✅ | 选项数组 |
| `value` | DynamicStringList | ✅ | 当前选中值 |
| `variant` | enum | ❌ | 选择模式 |
| `displayStyle` | enum | ❌ | 显示样式 |
| `filterable` | boolean | ❌ | 是否可过滤 |

**variant 可选值**：
| 值 | 说明 | 默认 |
|-----|------|-----|
| `multipleSelection` | 多选 | |
| `mutuallyExclusive` | 单选（互斥） | ✅ |

**displayStyle 可选值**：
| 值 | 说明 | 默认 |
|-----|------|-----|
| `checkbox` | 复选框样式 | ✅ |
| `chips` | 标签样式 | |

---

#### Slider - 滑块

```json
{
  "component": "Slider",
  "label": { "literalString": "用餐人数" },
  "value": { "literalNumber": 3 },
  "min": 1,
  "max": 10
}
```

| 属性 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `component` | const | ✅ | 固定值 "Slider" |
| `label` | DynamicString | ✅ | 滑块标签 |
| `value` | DynamicNumber | ✅ | 当前值 |
| `min` | number | ✅ | 最小值 |
| `max` | number | ✅ | 最大值 |

---

#### DateTimeInput - 日期时间输入

```json
{
  "component": "DateTimeInput",
  "value": { "path": "/form/datetime" },
  "enableDate": true,
  "enableTime": true,
  "label": { "literalString": "选择时间" }
}
```

| 属性 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `component` | const | ✅ | 固定值 "DateTimeInput" |
| `value` | DynamicString | ✅ | 当前值（ISO 8601） |
| `enableDate` | boolean | ❌ | 是否启用日期选择 |
| `enableTime` | boolean | ❌ | 是否启用时间选择 |
| `min` | DynamicString | ❌ | 最小日期/时间 |
| `max` | DynamicString | ❌ | 最大日期/时间 |
| `label` | DynamicString | ❌ | 标签 |

---

## 二、组件通用属性 (Component Common)

所有组件都继承以下通用属性：

| 属性 | 类型 | 说明 |
|------|------|------|
| `id` | string | 组件唯一ID |
| `weight` | number | 相对权重（Row/Column中有效） |

```json
{
  "id": "my_component",
  "component": "Text",
  "weight": 1,
  "text": { "literalString": "Hello" }
}
```

---

## 三、函数 (Functions)

### 3.1 验证函数

#### required - 必填验证

```json
{
  "call": "required",
  "args": { "value": { "path": "/form/name" } },
  "message": "姓名为必填项"
}
```

#### regex - 正则验证

```json
{
  "call": "regex",
  "args": {
    "value": { "path": "/form/phone" },
    "pattern": "^1[3-9]\\d{9}$"
  },
  "message": "请输入有效的手机号"
}
```

#### length - 长度验证

```json
{
  "call": "length",
  "args": {
    "value": { "path": "/form/password" },
    "min": 8,
    "max": 20
  },
  "message": "密码长度应为8-20个字符"
}
```

#### numeric - 数值范围

```json
{
  "call": "numeric",
  "args": {
    "value": { "path": "/form/age" },
    "min": 18,
    "max": 100
  },
  "message": "年龄应在18-100之间"
}
```

#### email - 邮箱验证

```json
{
  "call": "email",
  "args": { "value": { "path": "/form/email" } },
  "message": "请输入有效的邮箱地址"
}
```

---

### 3.2 格式化函数

#### formatString - 字符串格式化

```json
{
  "call": "formatString",
  "args": {
    "value": "欢迎, ${/user/name}! 当前余额: ${/user/balance}"
  }
}
```

**插值语法**：
- `${/absolute/path}` - 绝对路径
- `${relative/path}` - 相对路径
- `${function()}` - 函数调用

#### formatNumber - 数字格式化

```json
{
  "call": "formatNumber",
  "args": {
    "value": { "path": "/order/total" },
    "decimals": 2,
    "grouping": true
  }
}
```

#### formatCurrency - 货币格式化

```json
{
  "call": "formatCurrency",
  "args": {
    "value": { "path": "/order/amount" },
    "currency": "CNY",
    "decimals": 2
  }
}
```

#### formatDate - 日期格式化

```json
{
  "call": "formatDate",
  "args": {
    "value": { "path": "/order/date" },
    "format": "yyyy-MM-dd HH:mm"
  }
}
```

**format 模式参考**：
| 模式 | 说明 | 示例 |
|------|------|------|
| `yyyy` | 4位年份 | 2026 |
| `MM` | 2位月份 | 03 |
| `dd` | 2位日期 | 15 |
| `HH` | 24小时 | 18 |
| `mm` | 分钟 | 30 |
| `ss` | 秒数 | 00 |

#### pluralize - 复数形式

```json
{
  "call": "pluralize",
  "args": {
    "value": { "path": "/cart/count" },
    "zero": "0 件商品",
    "one": "1 件商品",
    "other": "${value} 件商品"
  }
}
```

---

### 3.3 动作函数

#### openUrl - 打开URL

```json
{
  "call": "openUrl",
  "args": {
    "url": "https://example.com"
  }
}
```

---

### 3.4 逻辑函数

#### and - 逻辑与

```json
{
  "call": "and",
  "args": {
    "values": [
      { "path": "/form/agree" },
      { "path": "/form/verified" }
    ]
  }
}
```

#### or - 逻辑或

```json
{
  "call": "or",
  "args": {
    "values": [
      { "path": "/form/email" },
      { "path": "/form/phone" }
    ]
  }
}
```

#### not - 逻辑非

```json
{
  "call": "not",
  "args": {
    "value": { "path": "/form/suspended" }
  }
}
```

---

## 四、主题系统 (Theme)

在 `createSurface` 消息中定义主题：

```json
{
  "version": "v0.10",
  "createSurface": {
    "surfaceId": "main",
    "catalogId": "https://a2ui.org/specification/v0_10/basic_catalog.json",
    "theme": {
      "primaryColor": "#FF5722",
      "iconUrl": "https://example.com/logo.png",
      "agentDisplayName": "餐厅助手"
    }
  }
}
```

| 属性 | 类型 | 说明 |
|------|------|------|
| `primaryColor` | string | 主色调（十六进制，如 #FF5722） |
| `iconUrl` | uri | 代理图标URL |
| `agentDisplayName` | string | 代理显示名称 |

---

## 五、组件使用示例

### 示例1：联系表单

```json
{
  "version": "v0.10",
  "updateComponents": {
    "surfaceId": "contact_form",
    "components": [
      {
        "id": "root",
        "component": "Card",
        "child": "form_content"
      },
      {
        "id": "form_content",
        "component": "Column",
        "children": ["title", "name_field", "email_field", "submit_btn"]
      },
      {
        "id": "title",
        "component": "Text",
        "text": { "literalString": "联系我们" },
        "variant": "h2"
      },
      {
        "id": "name_field",
        "component": "TextField",
        "label": { "literalString": "姓名" },
        "value": { "path": "/contact/name" },
        "variant": "shortText"
      },
      {
        "id": "email_field",
        "component": "TextField",
        "label": { "literalString": "邮箱" },
        "value": { "path": "/contact/email" },
        "variant": "shortText"
      },
      {
        "id": "submit_btn",
        "component": "Button",
        "child": "btn_text",
        "variant": "primary",
        "action": {
          "event": {
            "name": "submit_contact",
            "context": {}
          }
        }
      },
      {
        "id": "btn_text",
        "component": "Text",
        "text": { "literalString": "提交" }
      }
    ]
  }
}
```

### 示例2：餐厅列表

```json
{
  "version": "v0.10",
  "updateComponents": {
    "surfaceId": "restaurant_list",
    "components": [
      {
        "id": "root",
        "component": "Column",
        "children": ["header", "list"]
      },
      {
        "id": "header",
        "component": "Text",
        "text": { "literalString": "热门餐厅" },
        "variant": "h1"
      },
      {
        "id": "list",
        "component": "List",
        "children": {
          "template": {
            "componentId": "restaurant_card",
            "dataBinding": "/restaurants"
          }
        }
      },
      {
        "id": "restaurant_card",
        "component": "Card",
        "child": "card_content"
      },
      {
        "id": "card_content",
        "component": "Column",
        "children": ["name_text", "cuisine_text", "rating_row"]
      },
      {
        "id": "name_text",
        "component": "Text",
        "text": { "path": "name" },
        "variant": "h3"
      },
      {
        "id": "cuisine_text",
        "component": "Text",
        "text": { "path": "cuisine" }
      },
      {
        "id": "rating_row",
        "component": "Row",
        "children": ["star_icon", "rating_text"]
      },
      {
        "id": "star_icon",
        "component": "Icon",
        "name": { "literalString": "star" }
      },
      {
        "id": "rating_text",
        "component": "Text",
        "text": { "path": "rating" }
      }
    ]
  }
}
```

---

## 六、Schema 结构图

```
basic_catalog.json
├── components
│   ├── Text
│   ├── Image
│   ├── Icon
│   ├── Video
│   ├── AudioPlayer
│   ├── Row
│   ├── Column
│   ├── List
│   ├── Card
│   ├── Tabs
│   ├── Modal
│   ├── Divider
│   ├── Button
│   ├── TextField
│   ├── CheckBox
│   ├── ChoicePicker
│   ├── Slider
│   └── DateTimeInput
│
├── functions
│   ├── required (验证)
│   ├── regex (验证)
│   ├── length (验证)
│   ├── numeric (验证)
│   ├── email (验证)
│   ├── formatString (格式化)
│   ├── formatNumber (格式化)
│   ├── formatCurrency (格式化)
│   ├── formatDate (格式化)
│   ├── pluralize (格式化)
│   ├── openUrl (动作)
│   ├── and (逻辑)
│   ├── or (逻辑)
│   └── not (逻辑)
│
└── $defs
    ├── CatalogComponentCommon (weight属性)
    ├── theme (主题定义)
    ├── anyComponent (组件联合类型)
    └── anyFunction (函数联合类型)
```

---

## 总结

A2UI v0.10 的 `basic_catalog.json` 提供了：

1. **18种基础组件** - 覆盖所有常见UI场景
2. **14种内置函数** - 强大的验证和格式化能力
3. **灵活的数据绑定** - 支持 DynamicString、DynamicNumber、DynamicBoolean、DynamicStringList
4. **统一的主题系统** - 品牌定制能力
5. **类型安全的Schema** - 使用 JSON Schema 2020-12

这套组件系统足以构建复杂的AI驱动用户界面。
