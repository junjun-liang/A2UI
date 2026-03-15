# A2UI v0.10 客户端数据模型Schema详解

> 本文件详解 `/specification/v0_10/json/client_data_model.json`

---

## 概述

`client_data_model.json` 是 A2UI v0.10 中用于定义**客户端数据模型**的Schema。当客户端的UI表面启用 `sendDataModel` 功能时，客户端会在每次向服务器发送消息时附带当前的数据模型状态。

---

## Schema 结构

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "title": "A2UI Client Data Model Schema",
  "type": "object",
  "properties": {
    "version": { "const": "v0.10" },
    "surfaces": { "type": "object" }
  },
  "required": ["version", "surfaces"],
  "additionalProperties": false
}
```

---

## 字段定义

### 1. version - 版本标识

| 属性 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `version` | const | ✅ | 固定值 `"v0.10"` |

```json
{ "version": "v0.10" }
```

---

### 2. surfaces - 表面数据模型映射

| 属性 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `surfaces` | object | ✅ | 表面ID到数据模型的映射 |

**结构**：
```json
{
  "surfaces": {
    "surfaceId_1": { ... },
    "surfaceId_2": { ... }
  }
}
```

- **key**: 表面ID (`surfaceId`)
- **value**: 该表面的当前数据模型（标准JSON对象）

---

## 完整示例

### 示例1：简单表单数据

```json
{
  "version": "v0.10",
  "surfaces": {
    "contact_form": {
      "name": "张三",
      "email": "zhangsan@example.com",
      "phone": "13800138000",
      "message": "你好，我想咨询一下产品信息"
    }
  }
}
```

---

### 示例2：多表面数据模型

```json
{
  "version": "v0.10",
  "surfaces": {
    "header_surface": {
      "user": {
        "name": "李四",
        "avatar": "https://example.com/avatar.jpg"
      },
      "notifications": 3
    },
    "main_content": {
      "selectedCategory": "electronics",
      "filters": {
        "priceMin": 100,
        "priceMax": 5000,
        "sortBy": "rating"
      },
      "products": [
        { "id": "p1", "name": "手机", "price": 2999 },
        { "id": "p2", "name": "电脑", "price": 4999 }
      ]
    },
    "shopping_cart": {
      "items": [
        { "productId": "p1", "quantity": 1 },
        { "productId": "p2", "quantity": 2 }
      ],
      "total": 12997
    }
  }
}
```

---

### 示例3：带复杂数据结构的模型

```json
{
  "version": "v0.10",
  "surfaces": {
    "booking_form": {
      "customer": {
        "name": "王五",
        "email": "wangwu@example.com",
        "phone": "13900139000"
      },
      "booking": {
        "type": "restaurant",
        "date": "2026-03-20",
        "time": "18:30",
        "guests": 4,
        "preferences": {
          "smoking": false,
          "windowSeat": true,
          "dietary": ["无辣", "少油"]
        }
      },
      "selectedRestaurant": {
        "id": "r123",
        "name": "川香楼",
        "rating": 4.5,
        "address": {
          "street": "中山路123号",
          "city": "北京",
          "district": "朝阳区"
        }
      }
    }
  }
}
```

---

## 使用场景

### 场景1：用户提交表单

当用户点击提交按钮时，客户端发送：

```json
{
  "version": "v0.10",
  "action": {
    "name": "submit_contact_form",
    "surfaceId": "contact_form",
    "sourceComponentId": "submit_button",
    "timestamp": "2026-03-15T10:30:00Z",
    "context": {}
  }
}
```

**附带的数据模型**（在A2A metadata中）：

```json
{
  "version": "v0.10",
  "surfaces": {
    "contact_form": {
      "name": "张三",
      "email": "zhangsan@example.com",
      "phone": "13800138000",
      "message": "你好，我想咨询一下产品信息"
    }
  }
}
```

---

### 场景2：用户选择商品

```json
// 用户操作
{
  "version": "v0.10",
  "action": {
    "name": "add_to_cart",
    "surfaceId": "product_list",
    "sourceComponentId": "add_button_p1",
    "timestamp": "2026-03-15T11:00:00Z",
    "context": { "productId": "p1" }
  }
}

// 附带的数据模型
{
  "version": "v0.10",
  "surfaces": {
    "product_list": {
      "selectedCategory": "electronics",
      "filters": { "priceMin": 0, "priceMax": 10000 },
      "cart": {
        "items": [
          { "productId": "p1", "quantity": 1 }
        ]
      }
    }
  }
}
```

---

### 场景3：用户切换标签页

```json
// 用户操作
{
  "version": "v0.10",
  "action": {
    "name": "switch_tab",
    "surfaceId": "app_tabs",
    "sourceComponentId": "tab_settings",
    "timestamp": "2026-03-15T11:30:00Z",
    "context": { "tab": "settings" }
  }
}

// 附带的数据模型
{
  "version": "v0.10",
  "surfaces": {
    "app_tabs": {
      "activeTab": "settings",
      "settings": {
        "theme": "dark",
        "language": "zh-CN",
        "notifications": true
      }
    }
  }
}
```

---

## 与 sendDataModel 的关系

### 启用 sendDataModel

在 `createSurface` 消息中设置：

```json
{
  "version": "v0.10",
  "createSurface": {
    "surfaceId": "main_form",
    "catalogId": "https://a2ui.org/specification/v0_10/basic_catalog.json",
    "sendDataModel": true
  }
}
```

### 数据同步流程

```
Server                                    Client
  |                                          |
  |--- createSurface (sendDataModel: true) ->|
  |                                          |
  |<-- updateComponents ---------------------|
  |                                          |
  |<-- updateDataModel ---------------------|
  |                                          |
  |  (用户输入/交互...)                       |
  |                                          |
  |<-- action + clientDataModel (metadata) -|
  |   surfaces: {                           |
  |     main_form: { ... }                  |
  |   }                                     |
  |                                          |
  |--- (根据数据模型处理业务逻辑)             |
  |                                          |
```

---

## 关键特性

### 1. 目标定向

- 数据模型**仅发送给创建该表面的服务器**
- 不会泄露到其他代理或服务器

### 2. 触发条件

- 仅在用户触发**动作**时发送（如按钮点击）
- 被动数据变更（如输入框输入）**不会**触发发送

### 3. 数据完整性

- 发送的是**完整的**数据模型
- 服务器可以依赖此数据进行业务处理

---

## Schema 结构图

```
client_data_model.json
├── version (required)        - 固定值 "v0.10"
│   └── const: "v0.10"
│
└── surfaces (required)       - 表面数据模型映射
    └── [surfaceId]: { ... } - 每个表面的数据模型
        ├── 任意JSON对象
        ├── 可以嵌套
        └── 可以是任何有效JSON类型
```

---

## 与其他Schema的关系

| Schema | 关系 |
|--------|------|
| `server_to_client.json` | createSurface 中的 sendDataModel 字段启用此功能 |
| `client_capabilities.json` | 客户端声明支持此功能 |
| `client_to_server.json` | action 消息可附带此数据模型 |

---

## 总结

A2UI v0.10 的客户端数据模型Schema提供了：

1. **状态同步** - 服务器始终拥有客户端最新状态
2. **目标定向** - 数据仅发送给创建表面的服务器
3. **完整数据** - 每次发送完整的表面数据模型
4. **灵活结构** - 支持任意复杂的JSON数据结构

这套机制使得AI代理可以基于用户当前输入进行智能响应，实现真正的双向数据交互。
