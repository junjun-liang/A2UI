# A2UI 客户端到服务器事件Schema (v0.8)

> 本文件解读 `/specification/v0_8/json/client_to_server.json`

## 概述

这是 A2UI v0.8 版本的**客户端到服务器事件Schema**，定义了客户端（用户界面）向服务器（AI代理）发送事件消息的格式。

***

## Schema 结构

```json
{
  "title": "A2UI (Agent to UI) Client-to-Server Event Schema",
  "description": "Describes a JSON payload for a client-to-server event message.",
  "type": "object",
  "minProperties": 1,
  "maxProperties": 1,
  "properties": {
    "userAction": { ... },
    "error": { ... }
  },
  "oneOf": [
    { "required": ["userAction"] },
    { "required": ["error"] }
  ]
}
```

### 核心规则

- 消息必须包含 **且仅包含** 一个属性：`userAction` 或 `error`
- `minProperties: 1` 和 `maxProperties: 1` 确保单事件传输

***

## 1. userAction - 用户操作事件

当用户与UI组件交互时，客户端向服务器报告用户触发的操作。

### 字段定义

| 字段                  | 类型     |  必填 | 说明                                      |
| ------------------- | ------ | :-: | --------------------------------------- |
| `name`              | string |  ✅  | 操作名称（来自组件的 `action.name` 属性）            |
| `surfaceId`         | string |  ✅  | 事件来源的UI表面ID                             |
| `sourceComponentId` | string |  ✅  | 触发事件的组件ID                               |
| `timestamp`         | string |  ✅  | 事件发生的ISO 8601时间戳                        |
| `context`           | object |  ✅  | 键值对对象（来自组件的 `action.context`，已完成数据绑定解析） |

### 完整结构

```json
{
  "userAction": {
    "name": "submit_booking",
    "surfaceId": "booking_surface",
    "sourceComponentId": "submit_button",
    "timestamp": "2025-03-15T10:30:00Z",
    "context": {
      "formId": "restaurant_booking",
      "userName": "张三",
      "bookingDate": "2025-03-20"
    }
  }
}
```

### 字段详解

#### name

操作名称，必须与服务器端定义的组件 `action.name` 相匹配。

```json
{ "name": "submit_form" }
```

#### surfaceId

事件发生的UI表面唯一标识符。

```json
{ "surfaceId": "main_surface" }
```

#### sourceComponentId

触发事件的组件ID，通常是Button等交互组件。

```json
{ "sourceComponentId": "submit_button" }
```

#### timestamp

ISO 8601格式的时间戳。

```json
{ "timestamp": "2025-03-15T10:30:00Z" }
```

#### context

包含来自组件 `action.context` 的键值对，数据绑定已被解析为实际值。

```json
{
  "context": {
    "formId": "booking_form",
    "userId": "user_123",
    "amount": 100
  }
}
```

***

## 2. error - 错误事件

当客户端发生错误时，向服务器报告错误信息。

### 字段定义

| 字段   | 类型  |  必填 | 说明              |
| ---- | --- | :-: | --------------- |
| (任意) | any |  ❌  | 错误内容灵活，可包含任意键值对 |

### 特点

- `additionalProperties: true` - 允许任意结构
- 适用于报告各种客户端错误

### 示例

```json
{
  "error": {
    "code": "RENDER_ERROR",
    "message": "Failed to load image",
    "componentId": "image_avatar",
    "surfaceId": "profile_surface"
  }
}
```

***

## 与服务器到客户端的关系

A2UI采用**双向通信**机制：

```
┌─────────────┐                              ┌─────────────┐
│   Server    │                              │   Client    │
│  (Agent)    │                              │  (Browser)  │
└──────┬──────┘                              └──────┬──────┘
       │                                            │
       │  server_to_client.json                     │
       │  (A2UI消息: beginRendering,               │
       │   surfaceUpdate, dataModelUpdate,          │
       │   deleteSurface)                           │
       │ ─────────────────────────────────────────> │
       │                                            │
       │                                            │
       │  client_to_server.json                     │
       │  (用户操作: userAction, error)             │
       │ <───────────────────────────────────────── │
       │                                            │
```

### 对应关系

| 服务器 → 客户端         | 客户端 → 服务器    |
| ----------------- | ------------ |
| `beginRendering`  | -            |
| `surfaceUpdate`   | -            |
| `dataModelUpdate` | -            |
| `deleteSurface`   | -            |
| -                 | `userAction` |
| -                 | `error`      |

***

## 使用场景

### 场景1：表单提交

**服务器发送**：

```json
{
  "beginRendering": {
    "surfaceId": "contact_form",
    "root": "form_root"
  },
  "surfaceUpdate": {
    "surfaceId": "contact_form",
    "components": [
      {
        "id": "form_root",
        "component": {
          "Column": {
            "children": ["name_input", "email_input", "submit_btn"]
          }
        }
      },
      {
        "id": "name_input",
        "component": {
          "TextField": {
            "label": { "literalString": "姓名" }
          }
        }
      },
      {
        "id": "email_input",
        "component": {
          "TextField": {
            "label": { "literalString": "邮箱" }
          }
        }
      },
      {
        "id": "submit_btn",
        "component": {
          "Button": {
            "child": "btn_text",
            "action": {
              "name": "submit_contact_form",
              "context": [
                { "key": "formType", "value": { "literalString": "contact" } }
              ]
            }
          }
        }
      },
      {
        "id": "btn_text",
        "component": {
          "Text": { "text": { "literalString": "提交" } }
        }
      }
    ]
  }
}
```

**用户点击提交按钮后，客户端发送**：

```json
{
  "userAction": {
    "name": "submit_contact_form",
    "surfaceId": "contact_form",
    "sourceComponentId": "submit_btn",
    "timestamp": "2025-03-15T10:30:00Z",
    "context": {
      "formType": "contact",
      "name": "张三",
      "email": "zhangsan@example.com"
    }
  }
}
```

***

### 场景2：选择交互

**服务器发送**（带选项列表）：

```json
{
  "surfaceUpdate": {
    "surfaceId": "restaurant_selection",
    "components": [
      {
        "id": "cuisine_choice",
        "component": {
          "MultipleChoice": {
            "selections": { "path": "/selected_cuisines" },
            "options": [
              { "label": { "literalString": "川菜" }, "value": "sichuan" },
              { "label": { "literalString": "粤菜" }, "value": "cantonese" },
              { "label": { "literalString": "火锅" }, "value": "hotpot" }
            ],
            "maxAllowedSelections": 2
          }
        }
      }
    ]
  }
}
```

**用户选择后，客户端发送**：

```json
{
  "userAction": {
    "name": "update_cuisine_selection",
    "surfaceId": "restaurant_selection",
    "sourceComponentId": "cuisine_choice",
    "timestamp": "2025-03-15T10:35:00Z",
    "context": {
      "selectedValues": ["sichuan", "hotpot"]
    }
  }
}
```

***

### 场景3：错误报告

**客户端错误**：

```json
{
  "error": {
    "code": "IMAGE_LOAD_FAILED",
    "message": "无法加载图片: https://example.com/broken-link.jpg",
    "componentId": "hero_image",
    "surfaceId": "home_page",
    "timestamp": "2025-03-15T10:40:00Z"
  }
}
```

***

## 完整示例

### 完整的用户交互流程

**步骤1：服务器初始化UI**

```json
{
  "beginRendering": {
    "surfaceId": "booking_surface",
    "root": "root"
  },
  "surfaceUpdate": {
    "surfaceId": "booking_surface",
    "components": [
      { "id": "root", "component": { "Column": { "children": ["title", "guests", "date", "submit"] } } },
      { "id": "title", "component": { "Text": { "text": { "literalString": "餐厅预订" }, "usageHint": "h1" } } },
      { "id": "guests", "component": { "Slider": { "value": { "literalNumber": 2 }, "minValue": 1, "maxValue": 10 } } },
      { "id": "date", "component": { "DateTimeInput": { "value": { "path": "/booking/date" }, "enableDate": true } } },
      {
        "id": "submit",
        "component": {
          "Button": {
            "child": "btn_label",
            "action": {
              "name": "confirm_booking",
              "context": [
                { "key": "service", "value": { "literalString": "restaurant_booking" } }
              ]
            }
          }
        }
      },
      { "id": "btn_label", "component": { "Text": { "text": { "literalString": "确认预订" } } }
    ]
  }
}
```

**步骤2：用户点击确认按钮**

```json
{
  "userAction": {
    "name": "confirm_booking",
    "surfaceId": "booking_surface",
    "sourceComponentId": "submit",
    "timestamp": "2025-03-15T18:30:00Z",
    "context": {
      "service": "restaurant_booking",
      "guests": 4,
      "date": "2025-03-20"
    }
  }
}
```

**步骤3：服务器处理并更新UI**

```json
{
  "surfaceUpdate": {
    "surfaceId": "booking_surface",
    "components": [
      { "id": "title", "component": { "Text": { "text": { "literalString": "预订成功！" } } } },
      { "id": "submit", "component": { "Button": { "child": "btn_label", "action": { "name": "view_booking" } } } },
      { "id": "btn_label", "component": { "Text": { "text": { "literalString": "查看预订" } } } }
    ]
  }
}
```

***

## Schema 结构图

```
client_to_server.json
├── userAction (必选其一)
│   ├── name (required)          - 操作名称
│   ├── surfaceId (required)     - UI表面ID
│   ├── sourceComponentId (required) - 触发组件ID
│   ├── timestamp (required)     - ISO 8601时间戳
│   └── context (required)       - 解析后的上下文数据
│
└── error (必选其一)
    └── [任意键值对]               - 灵活的错误内容
```

***

## 总结

A2UI的客户端到服务器事件Schema设计简洁，主要用于：

1. **用户交互反馈**：将用户在UI上的操作传回服务器
2. **数据绑定更新**：通过context传递用户输入的数据
3. **错误报告**：客户端错误及时通知服务器

这种双向通信机制使得A2UI成为一个完整的交互式UI协议，支持构建丰富的AI驱动用户界面。
