# A2UI v0.10 通用类型定义Schema详解

> 本文件详解 `/specification/v0_10/json/common_types.json`

---

## 概述

`common_types.json` 是 A2UI v0.10 中定义**通用类型**的Schema。这些类型在整个A2UI协议中被广泛使用，提供了灵活的数据绑定、函数调用、动作定义等能力。

---

## Schema 结构

```json
{
  "$defs": {
    "ComponentId": { ... },
    "CallId": { ... },
    "AccessibilityAttributes": { ... },
    "ComponentCommon": { ... },
    "ChildList": { ... },
    "DataBinding": { ... },
    "DynamicValue": { ... },
    "DynamicString": { ... },
    "DynamicNumber": { ... },
    "DynamicBoolean": { ... },
    "DynamicStringList": { ... },
    "FunctionCall": { ... },
    "CheckRule": { ... },
    "Checkable": { ... },
    "Action": { ... }
  }
}
```

---

## 1. ComponentId - 组件ID

用于引用组件的唯一标识符。

```json
{ "type": "string" }
```

**说明**：组件ID是字符串类型，用于在同一表面内定义和引用组件。

---

## 2. CallId - 调用ID

服务器发起的函数调用的唯一标识符。

```json
{
  "agentId": "agent_1",
  "callId": "call_abc123"
}
```

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `agentId` | string | ❌ | 发起调用的代理ID |
| `callId` | string | ✅ | 函数调用实例的唯一ID |

---

## 3. AccessibilityAttributes - 无障碍属性

增强辅助技术的可访问性属性。

```json
{
  "label": { "literalString": "提交按钮" },
  "description": { "literalString": "点击提交表单" }
}
```

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `label` | DynamicString | ❌ | 简短描述（1-3个词） |
| `description` | DynamicString | ❌ | 额外说明信息 |

---

## 4. ComponentCommon - 组件通用属性

所有组件都继承的通用属性。

```json
{
  "id": "my_component",
  "accessibility": {
    "label": { "literalString": "标题" }
  }
}
```

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `id` | ComponentId | ✅ | 组件唯一ID |
| `accessibility` | AccessibilityAttributes | ❌ | 无障碍属性 |

---

## 5. ChildList - 子组件列表

定义组件的子组件，支持静态列表和动态模板两种方式。

### 5.1 静态列表

```json
["child1", "child2", "child3"]
```

### 5.2 动态模板

```json
{
  "componentId": "item_template",
  "path": "/items"
}
```

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `componentId` | ComponentId | ✅ | 模板组件ID |
| `path` | string | ✅ | 数据模型中的数组路径 |

---

## 6. DataBinding - 数据绑定

引用数据模型中的值。

```json
{ "path": "/user/name" }
```

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `path` | string | ✅ | JSON Pointer路径 |

---

## 7. DynamicValue - 动态值

可以是字面值、数据绑定或函数调用。

```json
// 字面值
"Hello"

// 数据绑定
{ "path": "/user/name" }

// 函数调用
{ "call": "formatString", "args": { "value": "Hello ${/name}" } }
```

**联合类型**：string | number | boolean | array | DataBinding | FunctionCall

---

## 8. DynamicString - 动态字符串

返回字符串类型的值。

```json
// 字面值
"Hello"

// 数据绑定
{ "path": "/user/name" }

// 函数调用（返回string）
{ "call": "formatString", "args": { "value": "Hello ${/name}" }, "returnType": "string" }
```

---

## 9. DynamicNumber - 动态数字

返回数字类型的值。

```json
// 字面值
42

// 数据绑定
{ "path": "/cart/total" }

// 函数调用（返回number）
{ "call": "numeric", "args": { "value": 100 }, "returnType": "number" }
```

---

## 10. DynamicBoolean - 动态布尔

返回布尔类型的值。

```json
// 字面值
true

// 数据绑定
{ "path": "/user/isActive" }

// 函数调用（返回boolean）
{ "call": "required", "args": { "value": "/email" }, "returnType": "boolean" }
```

---

## 11. DynamicStringList - 动态字符串列表

返回字符串数组的值。

```json
// 字面值数组
["option1", "option2"]

// 数据绑定
{ "path": "/form/selectedOptions" }

// 函数调用（返回array）
{ "call": "split", "args": { "value": "a,b,c" }, "returnType": "array" }
```

---

## 12. FunctionCall - 函数调用

调用客户端函数。

```json
{
  "callableFrom": "clientOrRemote",
  "call": "getLocation",
  "args": {
    "enableHighAccuracy": true
  },
  "returnType": "object"
}
```

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `callableFrom` | enum | ❌ | 调用来源 |
| `call` | string | ✅ | 函数名称 |
| `args` | object | ❌ | 函数参数 |
| `returnType` | enum | ❌ | 返回值类型 |

**callableFrom 可选值**：
| 值 | 说明 |
|-----|------|
| `clientOnly` | 仅客户端可调用 |
| `remoteOnly` | 仅服务器可调用 |
| `clientOrRemote` | 客户端和服务器都可调用 |

**returnType 可选值**：
| 值 | 说明 |
|-----|------|
| `array` | 数组 |
| `boolean` | 布尔值 |
| `number` | 数字 |
| `object` | 对象 |
| `string` | 字符串 |
| `void` | 无返回值 |

---

## 13. CheckRule - 验证规则

单个输入验证规则。

```json
{
  "condition": { "path": "/form/email" },
  "message": "邮箱为必填项"
}
```

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `condition` | DynamicBoolean | ✅ | 验证条件 |
| `message` | string | ✅ | 验证失败消息 |

---

## 14. Checkable - 可验证属性

支持客户端验证的组件属性。

```json
{
  "checks": [
    {
      "condition": { "call": "required", "args": { "value": { "path": "/form/email" } } },
      "message": "邮箱为必填项"
    },
    {
      "condition": { "call": "email", "args": { "value": { "path": "/form/email" } } },
      "message": "请输入有效的邮箱地址"
    }
  ]
}
```

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `checks` | array | ❌ | 验证规则数组 |

---

## 15. Action - 动作定义

定义交互处理器，可以触发服务器事件或执行本地函数。

### 15.1 触发服务器事件

```json
{
  "event": {
    "name": "submit_form",
    "context": {
      "userId": { "path": "/user/id" }
    }
  }
}
```

### 15.2 执行本地函数

```json
{
  "functionCall": {
    "call": "vibrate",
    "args": {
      "duration": 100
    },
    "returnType": "void"
  }
}
```

---

## 使用示例

### 示例1：完整组件定义

```json
{
  "id": "email_input",
  "accessibility": {
    "label": { "literalString": "邮箱输入框" },
    "description": { "literalString": "请输入有效的邮箱地址" }
  },
  "component": "TextField",
  "label": { "literalString": "邮箱" },
  "value": { "path": "/form/email" },
  "checks": [
    {
      "condition": { "call": "required", "args": { "value": { "path": "/form/email" } } },
      "message": "邮箱为必填项"
    },
    {
      "condition": { "call": "email", "args": { "value": { "path": "/form/email" } } },
      "message": "请输入有效的邮箱地址"
    }
  ]
}
```

### 示例2：带动作的按钮

```json
{
  "id": "submit_button",
  "component": "Button",
  "child": "button_text",
  "variant": "primary",
  "action": {
    "event": {
      "name": "submit_contact_form",
      "context": {
        "formId": "contact",
        "timestamp": { "call": "now", "returnType": "string" }
      }
    }
  }
}
```

### 示例3：动态列表

```json
{
  "id": "product_list",
  "component": "List",
  "children": {
    "template": {
      "componentId": "product_card",
      "path": "/products"
    }
  }
}
```

---

## Schema 结构图

```
common_types.json ($defs)
├── ComponentId                  - 组件ID (string)
├── CallId                       - 调用ID
│   ├── agentId (optional)
│   └── callId (required)
├── AccessibilityAttributes      - 无障碍属性
│   ├── label (optional)
│   └── description (optional)
├── ComponentCommon             - 组件通用属性
│   ├── id (required)
│   └── accessibility (optional)
├── ChildList                   - 子组件列表
│   ├── string[]               - 静态列表
│   └── object                 - 动态模板
│       ├── componentId
│       └── path
├── DataBinding                 - 数据绑定 { path: string }
├── DynamicValue                - 动态值 (联合类型)
├── DynamicString               - 动态字符串
├── DynamicNumber               - 动态数字
├── DynamicBoolean             - 动态布尔
├── DynamicStringList          - 动态字符串列表
├── FunctionCall               - 函数调用
│   ├── callableFrom
│   ├── call
│   ├── args
│   └── returnType
├── CheckRule                   - 验证规则
│   ├── condition
│   └── message
├── Checkable                   - 可验证属性
│   └── checks[]
└── Action                      - 动作定义
    ├── event (服务器事件)
    └── functionCall (本地函数)
```

---

## 与其他Schema的关系

| Schema | 关系 |
|--------|------|
| `server_to_client.json` | 使用 FunctionCall、Action 等 |
| `client_to_server.json` | 使用 CallId 等 |
| `basic_catalog.json` | 定义组件使用这些类型 |

---

## 总结

A2UI v0.10 的通用类型提供了：

1. **灵活的数据绑定** - 支持字面值、数据绑定、函数调用
2. **类型安全** - DynamicString/Number/Boolean/List 确保类型正确
3. **验证系统** - CheckRule 和 Checkable 支持客户端验证
4. **可访问性** - AccessibilityAttributes 支持辅助技术
5. **双向动作** - Action 支持服务器事件和本地函数调用

这些类型构成了A2UI协议的基础，使得UI定义既灵活又类型安全。
