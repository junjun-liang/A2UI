# basic_catalog.json 说明文档

## 文件概述

本文件定义了 A2UI 协议的**基础组件和函数目录**。它是一个统一的目录（Catalog），包含了所有标准 UI 组件（如文本、按钮、输入框、布局容器等）和内置函数（如校验、格式化、逻辑运算等）的定义。

## 数据结构

顶层包含以下主要部分：

| 部分 | 说明 |
|------|------|
| `components` | UI 组件定义集合 |
| `functions` | 内置函数定义集合 |
| `$defs` | 辅助定义（通用组件属性、主题、组件/函数联合类型） |

## 组件列表

### 展示类组件

| 组件名 | 说明 | 必填字段 |
|--------|------|----------|
| **Text** | 文本显示，支持简单 Markdown | `component`, `text` |
| **Image** | 图片显示 | `component`, `url` |
| **Icon** | 图标显示，支持预定义名称或自定义路径 | `component`, `name` |
| **Video** | 视频播放 | `component`, `url` |
| **AudioPlayer** | 音频播放 | `component`, `url` |
| **Divider** | 分隔线 | `component` |

### 布局类组件

| 组件名 | 说明 | 必填字段 |
|--------|------|----------|
| **Row** | 水平排列子组件 | `component`, `children` |
| **Column** | 垂直排列子组件 | `component`, `children` |
| **List** | 列表容器 | `component`, `children` |
| **Card** | 卡片容器（单子组件） | `component`, `child` |
| **Tabs** | 选项卡 | `component`, `tabs` |
| **Modal** | 模态对话框 | `component`, `trigger`, `content` |

### 输入类组件

| 组件名 | 说明 | 必填字段 |
|--------|------|----------|
| **Button** | 按钮，支持校验和动作 | `component`, `child`, `action` |
| **TextField** | 文本输入框 | `component`, `label` |
| **CheckBox** | 复选框 | `component`, `label`, `value` |
| **ChoicePicker** | 选项选择器 | `component`, `options`, `value` |
| **Slider** | 滑块 | `component`, `value`, `max` |
| **DateTimeInput** | 日期时间输入 | `component`, `value` |

## 组件通用属性 (CatalogComponentCommon)

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `weight` | number | 否 | 在 Row/Column 中的相对权重，类似 CSS `flex-grow` |

## 主题定义 ($defs.theme)

| 字段 | 类型 | 说明 |
|------|------|------|
| `primaryColor` | string | 主品牌色，十六进制格式（如 `#00BFFF`） |
| `iconUrl` | string (uri) | 代理或工具的图标 URL |
| `agentDisplayName` | string | 代理或工具的显示名称 |

## 函数列表

### 校验函数

| 函数名 | 返回类型 | 说明 | 必填参数 |
|--------|---------|------|----------|
| **required** | boolean | 检查值非空 | `value` |
| **regex** | boolean | 正则匹配 | `value`, `pattern` |
| **length** | boolean | 字符串长度约束 | `value`，至少提供 `min` 或 `max` |
| **numeric** | boolean | 数值范围约束 | `value`，至少提供 `min` 或 `max` |
| **email** | boolean | 邮箱格式验证 | `value` |

### 格式化函数

| 函数名 | 返回类型 | 说明 | 必填参数 |
|--------|---------|------|----------|
| **formatString** | string | 字符串插值，支持 `${expression}` 语法 | `value` |
| **formatNumber** | string | 数字格式化 | `value` |
| **formatCurrency** | string | 货币格式化 | `value`, `currency` |
| **formatDate** | string | 日期格式化（Unicode TR35 模式） | `value`, `format` |
| **pluralize** | string | 基于数量返回复数形式 | `value`, `other` |

### 逻辑函数

| 函数名 | 返回类型 | 说明 | 必填参数 |
|--------|---------|------|----------|
| **and** | boolean | 逻辑与（至少 2 个值） | `values` |
| **or** | boolean | 逻辑或（至少 2 个值） | `values` |
| **not** | boolean | 逻辑非 | `value` |

### 其他函数

| 函数名 | 返回类型 | 说明 | 必填参数 |
|--------|---------|------|----------|
| **openUrl** | void | 在浏览器中打开 URL | `url` |

## 示例

### Text 组件

```json
{
  "id": "title",
  "component": "Text",
  "text": "Welcome",
  "variant": "h1"
}
```

### Button 组件

```json
{
  "id": "submit_btn",
  "component": "Button",
  "child": "btn_label",
  "variant": "primary",
  "action": { "event": { "name": "submit" } }
}
```

### TextField 带校验

```json
{
  "id": "email_field",
  "component": "TextField",
  "label": "Email",
  "value": { "path": "/contact/email" },
  "checks": [
    {
      "condition": {
        "call": "email",
        "args": { "value": { "path": "/contact/email" } },
        "returnType": "boolean"
      },
      "message": "Please enter a valid email"
    }
  ]
}
```
