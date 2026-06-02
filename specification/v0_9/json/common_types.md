# common_types.json 说明文档

## 文件概述

本文件定义了 A2UI 各 Schema 中共用的类型定义。这些类型被 `basic_catalog.json`、`server_to_client.json` 等核心协议文件引用，是 A2UI 协议的基础构建块。

## 数据结构

顶层为 JSON Schema 对象，所有公共类型定义在 `$defs` 中，包括组件 ID、无障碍属性、动态值类型、函数调用、校验规则和动作定义等。

## 字段说明

### ComponentId

| 字段名 | 类型 | 说明 |
|--------|------|------|
| （自身） | string | 组件的唯一标识符，用于同一界面内的组件定义和引用 |

### AccessibilityAttributes（无障碍属性）

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| label | DynamicString | 否 | 简短标签（通常 1-3 个词），供辅助技术传达元素用途，如输入框的"用户 ID" |
| description | DynamicString | 否 | 附加说明信息，如静音按钮的描述"将此对话的通知静音" |

### ComponentCommon（组件公共属性）

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| id | ComponentId | 是 | 组件唯一标识符 |
| accessibility | AccessibilityAttributes | 否 | 无障碍属性 |

### ChildList（子组件列表）

支持两种形式：

| 形式 | 类型 | 说明 |
|------|------|------|
| 静态列表 | ComponentId[] | 固定的子组件 ID 数组 |
| 动态模板 | object | 从数据模型列表动态生成子组件，包含 `componentId`（模板组件 ID）和 `path`（数据模型中列表路径） |

### DataBinding（数据绑定）

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| path | string | 是 | 指向数据模型中某个值的 JSON Pointer 路径 |

### DynamicValue（动态值）

可以是以下类型之一：

| 类型 | 说明 |
|------|------|
| string | 字符串字面量 |
| number | 数字字面量 |
| boolean | 布尔字面量 |
| array | 数组字面量 |
| DataBinding | 数据绑定路径 |
| FunctionCall | 函数调用 |

### DynamicString（动态字符串）

| 类型 | 说明 |
|------|------|
| string | 字符串字面量 |
| DataBinding | 数据绑定路径 |
| FunctionCall (returnType: "string") | 返回字符串的函数调用 |

### DynamicNumber（动态数字）

| 类型 | 说明 |
|------|------|
| number | 数字字面量 |
| DataBinding | 数据绑定路径 |
| FunctionCall (returnType: "number") | 返回数字的函数调用 |

### DynamicBoolean（动态布尔值）

| 类型 | 说明 |
|------|------|
| boolean | 布尔字面量 |
| DataBinding | 数据绑定路径 |
| FunctionCall (returnType: "boolean") | 返回布尔值的函数调用 |

### DynamicStringList（动态字符串列表）

| 类型 | 说明 |
|------|------|
| string[] | 字符串数组字面量 |
| DataBinding | 数据绑定路径 |
| FunctionCall (returnType: "array") | 返回字符串数组的函数调用 |

### FunctionCall（函数调用）

| 字段名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| call | string | 是 | - | 要调用的函数名称 |
| args | object | 否 | - | 传递给函数的参数，值为 DynamicValue 或字面对象 |
| returnType | string | 否 | "boolean" | 函数的预期返回类型，可选值：string、number、boolean、array、object、any、void |

### CheckRule（校验规则）

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| condition | DynamicBoolean | 是 | 校验条件，返回布尔值表示是否通过 |
| message | string | 是 | 校验失败时显示的错误消息 |

### Checkable（可校验属性）

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| checks | CheckRule[] | 否 | 校验规则列表，函数调用必须返回布尔值表示有效性 |

### Action（动作）

支持两种形式：

| 形式 | 字段 | 说明 |
|------|------|------|
| 服务端事件 | event | 触发服务端事件，包含 `name`（动作名称）和可选的 `context`（上下文键值对） |
| 客户端函数 | functionCall | 执行本地客户端函数 |

#### event 对象

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| name | string | 是 | 分派到服务端的动作名称 |
| context | object | 否 | 动作上下文的键值对，值可以是字面量或数据绑定路径 |

## 示例

### 数据绑定

```json
{ "path": "/user/name" }
```

### 函数调用

```json
{
  "call": "required",
  "args": { "value": { "path": "/username" } },
  "returnType": "boolean"
}
```

### 服务端事件动作

```json
{
  "event": {
    "name": "submitLogin",
    "context": {
      "username": { "path": "/username" },
      "remember": true
    }
  }
}
```

### 校验规则

```json
{
  "condition": {
    "call": "length",
    "args": { "value": { "path": "/password" }, "min": 8 },
    "returnType": "boolean"
  },
  "message": "密码长度不能少于8位"
}
```
