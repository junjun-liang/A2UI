# minimal_catalog.json 说明文档

## 文件概述

本文件定义了 A2UI 最小化目录（Minimal Catalog），是一个用于测试渲染器的精简目录。它仅包含最基础的组件和函数，足以验证 A2UI 渲染器的核心功能。目录 ID 为 `https://a2ui.org/specification/v0_9/catalogs/minimal/minimal_catalog.json`。

## 数据结构

顶层为目录对象，包含 `catalogId`、`components`、`functions` 和 `$defs` 四个主要部分。与 `basic_catalog.json` 相比，组件和函数数量大幅精简。

## 组件定义（components）

### Text（文本）

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| component | "Text" | 是 | 组件类型标识 |
| id | string | 是 | 组件唯一标识符 |
| text | DynamicString | 是 | 要显示的文本内容 |
| variant | string | 否 | 文本样式提示，可选值：h1、h2、h3、h4、h5、caption、body |
| weight | number | 否 | 相对权重 |

### Row（行布局）

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| component | "Row" | 是 | 组件类型标识 |
| id | string | 是 | 组件唯一标识符 |
| children | ChildList | 是 | 子组件列表 |
| justify | string | 否 | 主轴排列方式，可选值：center、end、spaceAround、spaceBetween、spaceEvenly、start、stretch |
| align | string | 否 | 交叉轴对齐方式，可选值：start、center、end、stretch |
| weight | number | 否 | 相对权重 |

### Column（列布局）

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| component | "Column" | 是 | 组件类型标识 |
| id | string | 是 | 组件唯一标识符 |
| children | ChildList | 是 | 子组件列表 |
| justify | string | 否 | 主轴排列方式，可选值：start、center、end、spaceBetween、spaceAround、spaceEvenly、stretch |
| align | string | 否 | 交叉轴对齐方式，可选值：center、end、start、stretch |
| weight | number | 否 | 相对权重 |

### Button（按钮）

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| component | "Button" | 是 | 组件类型标识 |
| id | string | 是 | 组件唯一标识符 |
| child | ComponentId | 是 | 子组件 ID（通常为 Text 组件） |
| variant | string | 否 | 按钮样式，可选值：primary、borderless |
| action | Action | 是 | 按钮动作 |
| checks | CheckRule[] | 否 | 校验规则列表 |
| weight | number | 否 | 相对权重 |

### TextField（文本输入框）

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| component | "TextField" | 是 | 组件类型标识 |
| id | string | 是 | 组件唯一标识符 |
| label | DynamicString | 是 | 输入框标签 |
| value | DynamicString | 否 | 输入框当前值 |
| variant | string | 否 | 输入框类型，可选值：longText、number、shortText、obscured |
| validationRegexp | string | 否 | 客户端验证正则表达式 |
| checks | CheckRule[] | 否 | 校验规则列表 |
| weight | number | 否 | 相对权重 |

## 函数定义（functions）

| 函数名 | 说明 | 必填参数 | 返回类型 |
|--------|------|----------|----------|
| capitalize | 将输入字符串转换为首字母大写形式 | value | string |

## 主题定义（theme）

| 属性名 | 类型 | 说明 |
|--------|------|------|
| primaryColor | string (hex) | 主品牌色，格式如 `#00BFFF` |

## 与 basic_catalog 的区别

- 组件数量：5 个（Text、Row、Column、Button、TextField）vs basic_catalog 的 18 个
- 函数数量：1 个（capitalize）vs basic_catalog 的 14 个
- 主题属性：仅 primaryColor vs basic_catalog 的 3 个
- 适用于渲染器基础功能测试和快速验证
