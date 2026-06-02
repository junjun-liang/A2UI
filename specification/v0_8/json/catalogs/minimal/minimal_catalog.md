# minimal_catalog.json

## 文件概述

A2UI 最小化组件目录定义。该目录是标准目录的子集，仅包含最基本的 UI 组件：Text、Row、Column、Button 和 TextField。适用于资源受限的客户端或需要最简 UI 的场景。

## 数据结构

顶层包含三个部分：

- `catalogId` - 目录唯一标识符
- `components` - 组件定义映射
- `styles` - 样式定义映射

## 字段说明

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| catalogId | string | 是 | 值为 https://a2ui.org/specification/v0_8/catalogs/minimal/minimal_catalog.json |
| components | object | 是 | 组件定义集合 |
| styles | object | 是 | 样式定义集合 |

## 包含的组件

| 组件名 | 说明 | 必填属性 |
|--------|------|----------|
| Text | 文本显示组件 | text |
| Row | 水平布局容器 | children |
| Column | 垂直布局容器 | children |
| Button | 按钮组件 | child, action |
| TextField | 文本输入组件 | label |

### Text 组件

| 属性名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| text | object | 是 | 文本内容，支持 literalString 或 path |
| usageHint | string | 否 | 样式提示：h1/h2/h3/h4/h5/caption/body |

### Row 组件

| 属性名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| children | object | 是 | 子组件定义，支持 explicitList 或 template |
| distribution | string | 否 | 主轴排列：center/end/spaceAround/spaceBetween/spaceEvenly/start |
| alignment | string | 否 | 交叉轴对齐：start/center/end/stretch |

### Column 组件

| 属性名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| children | object | 是 | 子组件定义，支持 explicitList 或 template |
| distribution | string | 否 | 主轴排列：start/center/end/spaceBetween/spaceAround/spaceEvenly |
| alignment | string | 否 | 交叉轴对齐：center/end/start/stretch |

### Button 组件

| 属性名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| child | string | 是 | 按钮内显示的组件 ID |
| primary | boolean | 否 | 是否为主要操作按钮 |
| action | object | 是 | 点击动作，包含 name 和可选 context |

### TextField 组件

| 属性名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| label | object | 是 | 输入框标签 |
| text | object | 否 | 输入框值 |
| textFieldType | string | 否 | 输入类型：date/longText/number/shortText/obscured |
| validationRegexp | string | 否 | 验证正则表达式 |

## 样式定义

| 样式名 | 类型 | 说明 |
|--------|------|------|
| font | string | UI 主字体 |
| primaryColor | string | 主色调，十六进制格式 |

## 与标准目录的区别

最小化目录不包含以下标准目录组件：Image、Icon、Video、AudioPlayer、List、Card、Tabs、Divider、Modal、CheckBox、DateTimeInput、MultipleChoice、Slider
