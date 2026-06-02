# basic_catalog.json 说明文档

## 文件概述

本文件定义了 A2UI 基础目录（Basic Catalog），是 A2UI 协议的核心组件和函数目录。它包含了所有基础 UI 组件的定义（如文本、按钮、输入框、布局组件等）以及一组客户端函数（如校验、格式化、逻辑运算等）。目录 ID 为 `https://a2ui.org/specification/v0_9/basic_catalog.json`。

## 数据结构

顶层为目录对象，包含 `catalogId`、`components`、`functions` 和 `$defs` 四个主要部分。

## 组件定义（components）

### Text（文本）

| 字段名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| component | "Text" | 是 | - | 组件类型标识 |
| id | string | 是 | - | 组件唯一标识符 |
| text | DynamicString | 是 | - | 要显示的文本内容，支持简单 Markdown 格式 |
| variant | string | 否 | "body" | 文本样式提示，可选值：h1、h2、h3、h4、h5、caption、body |
| accessibility | AccessibilityAttributes | 否 | - | 无障碍属性 |
| weight | number | 否 | - | 在 Row/Column 中的相对权重（类似 flex-grow） |

### Image（图片）

| 字段名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| component | "Image" | 是 | - | 组件类型标识 |
| id | string | 是 | - | 组件唯一标识符 |
| url | DynamicString | 是 | - | 图片 URL |
| fit | string | 否 | "fill" | 图片缩放方式（对应 CSS object-fit），可选值：contain、cover、fill、none、scaleDown |
| variant | string | 否 | "mediumFeature" | 图片尺寸和样式提示，可选值：icon、avatar、smallFeature、mediumFeature、largeFeature、header |
| weight | number | 否 | - | 相对权重 |

### Icon（图标）

| 字段名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| component | "Icon" | 是 | - | 组件类型标识 |
| id | string | 是 | - | 组件唯一标识符 |
| name | string \| {path: string} | 是 | - | 图标名称（预定义枚举）或自定义 SVG 路径对象 |
| weight | number | 否 | - | 相对权重 |

预定义图标名称包括：accountCircle、add、arrowBack、arrowForward、attachFile、calendarToday、call、camera、check、close、delete、download、edit、event、error、fastForward、favorite、favoriteOff、folder、help、home、info、locationOn、lock、lockOpen、mail、menu、moreVert、moreHoriz、notificationsOff、notifications、pause、payment、person、phone、photo、play、print、refresh、rewind、search、send、settings、share、shoppingCart、skipNext、skipPrevious、star、starHalf、starOff、stop、upload、visibility、visibilityOff、volumeDown、volumeMute、volumeOff、volumeUp、warning。

### Video（视频）

| 字段名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| component | "Video" | 是 | - | 组件类型标识 |
| id | string | 是 | - | 组件唯一标识符 |
| url | DynamicString | 是 | - | 视频 URL |
| weight | number | 否 | - | 相对权重 |

### AudioPlayer（音频播放器）

| 字段名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| component | "AudioPlayer" | 是 | - | 组件类型标识 |
| id | string | 是 | - | 组件唯一标识符 |
| url | DynamicString | 是 | - | 音频 URL |
| description | DynamicString | 否 | - | 音频描述（如标题或摘要） |
| weight | number | 否 | - | 相对权重 |

### Row（行布局）

| 字段名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| component | "Row" | 是 | - | 组件类型标识 |
| id | string | 是 | - | 组件唯一标识符 |
| children | ChildList | 是 | - | 子组件列表（静态 ID 数组或动态模板） |
| justify | string | 否 | "start" | 主轴（水平）排列方式，可选值：center、end、spaceAround、spaceBetween、spaceEvenly、start、stretch |
| align | string | 否 | "stretch" | 交叉轴（垂直）对齐方式，可选值：start、center、end、stretch |
| weight | number | 否 | - | 相对权重 |

### Column（列布局）

| 字段名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| component | "Column" | 是 | - | 组件类型标识 |
| id | string | 是 | - | 组件唯一标识符 |
| children | ChildList | 是 | - | 子组件列表（静态 ID 数组或动态模板） |
| justify | string | 否 | "start" | 主轴（垂直）排列方式，可选值：start、center、end、spaceBetween、spaceAround、spaceEvenly、stretch |
| align | string | 否 | "stretch" | 交叉轴（水平）对齐方式，可选值：center、end、start、stretch |
| weight | number | 否 | - | 相对权重 |

### List（列表）

| 字段名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| component | "List" | 是 | - | 组件类型标识 |
| id | string | 是 | - | 组件唯一标识符 |
| children | ChildList | 是 | - | 子组件列表 |
| direction | string | 否 | "vertical" | 列表项排列方向，可选值：vertical、horizontal |
| align | string | 否 | "stretch" | 交叉轴对齐方式，可选值：start、center、end、stretch |
| weight | number | 否 | - | 相对权重 |

### Card（卡片）

| 字段名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| component | "Card" | 是 | - | 组件类型标识 |
| id | string | 是 | - | 组件唯一标识符 |
| child | ComponentId | 是 | - | 卡片内唯一子组件的 ID，多个元素需用布局组件包裹 |
| weight | number | 否 | - | 相对权重 |

### Tabs（选项卡）

| 字段名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| component | "Tabs" | 是 | - | 组件类型标识 |
| id | string | 是 | - | 组件唯一标识符 |
| tabs | object[] | 是 | - | 选项卡数组，每项包含 `title`（DynamicString，标签标题）和 `child`（ComponentId，子组件 ID） |
| weight | number | 否 | - | 相对权重 |

### Modal（模态框）

| 字段名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| component | "Modal" | 是 | - | 组件类型标识 |
| id | string | 是 | - | 组件唯一标识符 |
| trigger | ComponentId | 是 | - | 触发模态框的组件 ID（如按钮） |
| content | ComponentId | 是 | - | 模态框内显示的组件 ID |
| weight | number | 否 | - | 相对权重 |

### Divider（分隔线）

| 字段名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| component | "Divider" | 是 | - | 组件类型标识 |
| id | string | 是 | - | 组件唯一标识符 |
| axis | string | 否 | "horizontal" | 分隔线方向，可选值：horizontal、vertical |
| weight | number | 否 | - | 相对权重 |

### Button（按钮）

| 字段名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| component | "Button" | 是 | - | 组件类型标识 |
| id | string | 是 | - | 组件唯一标识符 |
| child | ComponentId | 是 | - | 子组件 ID，通常使用 Text 组件作为标签 |
| variant | string | 否 | "default" | 按钮样式，可选值：default、primary、borderless |
| action | Action | 是 | - | 按钮动作（服务端事件或客户端函数） |
| checks | CheckRule[] | 否 | - | 校验规则列表 |
| weight | number | 否 | - | 相对权重 |

### TextField（文本输入框）

| 字段名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| component | "TextField" | 是 | - | 组件类型标识 |
| id | string | 是 | - | 组件唯一标识符 |
| label | DynamicString | 是 | - | 输入框的文本标签 |
| value | DynamicString | 否 | - | 输入框的当前值 |
| variant | string | 否 | "shortText" | 输入框类型，可选值：longText、number、shortText、obscured |
| validationRegexp | string | 否 | - | 用于客户端验证的正则表达式 |
| checks | CheckRule[] | 否 | - | 校验规则列表 |
| weight | number | 否 | - | 相对权重 |

### CheckBox（复选框）

| 字段名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| component | "CheckBox" | 是 | - | 组件类型标识 |
| id | string | 是 | - | 组件唯一标识符 |
| label | DynamicString | 是 | - | 复选框旁显示的文本 |
| value | DynamicBoolean | 是 | - | 复选框状态（true 为选中） |
| checks | CheckRule[] | 否 | - | 校验规则列表 |
| weight | number | 否 | - | 相对权重 |

### ChoicePicker（选择器）

| 字段名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| component | "ChoicePicker" | 是 | - | 组件类型标识 |
| id | string | 是 | - | 组件唯一标识符 |
| label | DynamicString | 否 | - | 选项组的标签 |
| variant | string | 否 | "mutuallyExclusive" | 选择模式，可选值：multipleSelection、mutuallyExclusive |
| options | object[] | 是 | - | 选项列表，每项包含 `label`（DynamicString）和 `value`（string） |
| value | DynamicStringList | 是 | - | 当前选中值列表，应绑定到数据模型中的字符串数组 |
| displayStyle | string | 否 | "checkbox" | 显示样式，可选值：checkbox、chips |
| filterable | boolean | 否 | false | 是否显示搜索输入框以过滤选项 |
| checks | CheckRule[] | 否 | - | 校验规则列表 |
| weight | number | 否 | - | 相对权重 |

### Slider（滑块）

| 字段名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| component | "Slider" | 是 | - | 组件类型标识 |
| id | string | 是 | - | 组件唯一标识符 |
| label | DynamicString | 否 | - | 滑块标签 |
| min | number | 否 | 0 | 最小值 |
| max | number | 是 | - | 最大值 |
| value | DynamicNumber | 是 | - | 当前值 |
| checks | CheckRule[] | 否 | - | 校验规则列表 |
| weight | number | 否 | - | 相对权重 |

### DateTimeInput（日期时间输入）

| 字段名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| component | "DateTimeInput" | 是 | - | 组件类型标识 |
| id | string | 是 | - | 组件唯一标识符 |
| value | DynamicString | 是 | - | 选中的日期/时间值（ISO 8601 格式），未设置时初始化为空字符串 |
| enableDate | boolean | 否 | false | 是否允许选择日期 |
| enableTime | boolean | 否 | false | 是否允许选择时间 |
| min | DynamicString | 否 | - | 允许的最小日期/时间（ISO 8601 格式） |
| max | DynamicString | 否 | - | 允许的最大日期/时间（ISO 8601 格式） |
| label | DynamicString | 否 | - | 输入框标签 |
| checks | CheckRule[] | 否 | - | 校验规则列表 |
| weight | number | 否 | - | 相对权重 |

## 函数定义（functions）

### 校验函数

| 函数名 | 说明 | 必填参数 | 返回类型 |
|--------|------|----------|----------|
| required | 检查值非空 | value | boolean |
| regex | 检查值匹配正则表达式 | value, pattern | boolean |
| length | 检查字符串长度约束 | value, min?, max? | boolean |
| numeric | 检查数值范围约束 | value, min?, max? | boolean |
| email | 检查值是否为有效邮箱地址 | value | boolean |

### 格式化函数

| 函数名 | 说明 | 必填参数 | 返回类型 |
|--------|------|----------|----------|
| formatString | 字符串插值，支持 `${expression}` 格式 | value | string |
| formatNumber | 格式化数字（分组和精度） | value, decimals?, grouping? | string |
| formatCurrency | 格式化货币 | value, currency, decimals?, grouping? | string |
| formatDate | 格式化日期时间（Unicode TR35 模式） | value, format | string |
| pluralize | 基于 CLDR 复数类别返回本地化字符串 | value, other, zero?, one?, two?, few?, many? | string |

### 操作函数

| 函数名 | 说明 | 必填参数 | 返回类型 |
|--------|------|----------|----------|
| openUrl | 在浏览器中打开 URL | url | void |

### 逻辑函数

| 函数名 | 说明 | 必填参数 | 返回类型 |
|--------|------|----------|----------|
| and | 逻辑与运算 | values (至少2个) | boolean |
| or | 逻辑或运算 | values (至少2个) | boolean |
| not | 逻辑非运算 | value | boolean |

## 主题定义（theme）

| 属性名 | 类型 | 说明 |
|--------|------|------|
| primaryColor | string (hex) | 主品牌色，用于高亮（如主按钮、活动边框），格式如 `#00BFFF` |
| iconUrl | string (uri) | 标识 Agent 或工具的图片 URL |
| agentDisplayName | string | 在界面旁显示的 Agent 或工具名称 |

## 示例

### 使用 Text 和 Button 组件

```json
{
  "id": "root",
  "component": "Column",
  "children": ["title", "submitBtn"],
  "justify": "center",
  "align": "center"
}
```

### 使用 formatDate 函数

```json
{
  "call": "formatDate",
  "args": {
    "value": { "path": "/createdAt" },
    "format": "MMM dd, yyyy"
  },
  "returnType": "string"
}
```
