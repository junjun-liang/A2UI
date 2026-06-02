# common_types.json 说明文档

## 文件概述

本文件定义了 A2UI 协议中跨多个模式共用的**公共类型**。这些类型被 `server_to_client.json`、`basic_catalog.json` 等文件引用，提供组件 ID、数据绑定、动态值、函数调用、校验规则和动作处理等基础定义。

## 数据结构

顶层结构为 JSON Schema 的 `$defs` 对象，包含以下类型定义：

| 类型名 | 说明 |
|--------|------|
| `ComponentId` | 组件唯一标识符 |
| `CallId` | 函数调用唯一标识符 |
| `AccessibilityAttributes` | 无障碍属性 |
| `ComponentCommon` | 组件通用属性 |
| `ChildList` | 子组件列表（静态或动态） |
| `DataBinding` | 数据绑定路径 |
| `DynamicValue` | 动态值（字面量/数据绑定/函数调用） |
| `DynamicString` | 动态字符串 |
| `DynamicNumber` | 动态数字 |
| `DynamicBoolean` | 动态布尔值 |
| `DynamicStringList` | 动态字符串数组 |
| `FunctionCall` | 函数调用定义 |
| `CheckRule` | 校验规则 |
| `Checkable` | 可校验组件属性 |
| `Action` | 交互动作处理 |

## 字段说明

### ComponentId

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| _(自身)_ | string | — | 组件的唯一标识符，用于同一表面内的组件定义和引用 |

### CallId

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `agentId` | string | 否 | 发起函数调用的代理标识 |
| `callId` | string | 是 | 函数调用实例的唯一标识 |

### AccessibilityAttributes

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `label` | DynamicString | 否 | 简短标签（1-3 个词），供辅助技术传达元素用途 |
| `description` | DynamicString | 否 | 额外描述信息，如操作说明或格式要求 |

### ComponentCommon

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `id` | ComponentId | 是 | 组件唯一标识符 |
| `accessibility` | AccessibilityAttributes | 否 | 无障碍属性 |

### ChildList

支持两种形式：

**静态列表：** 字符串数组，每个元素为子组件 ID。

**动态模板：**

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `componentId` | ComponentId | 是 | 用作模板的组件 ID |
| `path` | string | 是 | 数据模型中组件属性对象列表的路径 |

### DataBinding

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `path` | string | 是 | 指向数据模型中某个值的 JSON Pointer 路径 |

### DynamicValue

可以是以下类型之一：`string`、`number`、`boolean`、`array`、`DataBinding`、`FunctionCall`。

### DynamicString / DynamicNumber / DynamicBoolean

分别为对应类型的动态值：字面量、数据绑定路径或返回对应类型的函数调用。

### DynamicStringList

可以是字符串数组、数据绑定路径或返回数组的函数调用。

### FunctionCall

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `call` | string | 是 | 要调用的函数名称 |
| `args` | object | 否 | 传递给函数的参数，值为 DynamicValue 或字面对象 |
| `returnType` | enum | 否 | 返回类型：`array`/`boolean`/`number`/`object`/`string`/`void`，默认 `boolean` |
| `callableFrom` | enum | 否 | 调用来源：`clientOnly`/`remoteOnly`/`clientOrRemote`，默认 `clientOnly` |

### CheckRule

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `condition` | DynamicBoolean | 是 | 校验条件，必须返回布尔值 |
| `message` | string | 是 | 校验失败时显示的错误消息 |

### Checkable

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `checks` | array (CheckRule) | 否 | 校验规则列表 |

### Action

支持两种形式：

**服务端事件：**

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `event` | object | 是 | 要派发到服务端的事件 |
| `event.name` | string | 是 | 动作名称 |
| `event.context` | object | 否 | 动作上下文的键值对，值为 DynamicValue |

**客户端函数调用：**

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `functionCall` | FunctionCall | 是 | 要执行的客户端函数 |

## 示例

### 数据绑定

```json
{ "path": "/user/name" }
```

### 函数调用

```json
{
  "call": "required",
  "args": { "value": { "path": "/formData/email" } },
  "returnType": "boolean"
}
```

### 校验规则

```json
{
  "condition": {
    "call": "email",
    "args": { "value": { "path": "/contact/email" } },
    "returnType": "boolean"
  },
  "message": "请输入有效的邮箱地址"
}
```

### 动作（服务端事件）

```json
{
  "event": {
    "name": "submitForm",
    "context": { "userId": { "path": "/user/id" } }
  }
}
```
