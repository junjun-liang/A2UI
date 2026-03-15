# A2UI Message Schema (v0.8) 详解

> 本文件解读 `/specification/v0_8/json/server_to_client.json` 中的JSON Schema定义

## 概述

这是一个 **A2UI (Agent to User Interface) 消息Schema** 定义文件，用于描述AI代理向客户端动态构建和更新用户界面的JSON格式。

### 核心概念

该Schema定义了 **四种主要的UI操作**，每条消息必须包含且仅包含其中一种操作：

| 操作 | 作用 |
|------|------|
| `beginRendering` | 开始渲染一个新的UI表面 |
| `surfaceUpdate` | 更新UI表面的组件列表 |
| `dataModelUpdate` | 更新UI表面的数据模型 |
| `deleteSurface` | 删除指定的UI表面 |

---

## 1. beginRendering - 开始渲染

**作用**：通知客户端开始渲染一个新的UI表面（Surface）

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `surfaceId` | string | ✅ | UI表面的唯一标识符 |
| `catalogId` | string | ❌ | 组件目录ID，默认使用标准目录 |
| `root` | string | ✅ | 根组件的ID |
| `styles` | object | ❌ | 样式信息 |

### JSON Schema 定义

```json
{
  "beginRendering": {
    "type": "object",
    "properties": {
      "surfaceId": { "type": "string" },
      "catalogId": { "type": "string" },
      "root": { "type": "string" },
      "styles": { "type": "object" }
    },
    "required": ["root", "surfaceId"]
  }
}
```

### 使用示例

```json
{
  "beginRendering": {
    "surfaceId": "main_surface",
    "catalogId": "standard_catalog",
    "root": "root_component",
    "styles": { "theme": "dark", "backgroundColor": "#ffffff" }
  }
}
```

---

## 2. surfaceUpdate - 更新表面

**作用**：用新的组件列表更新指定的UI表面

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `surfaceId` | string | ✅ | UI表面的唯一标识符 |
| `components` | array | ✅ | 组件列表，至少包含1个组件 |

### 组件结构

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `id` | string | ✅ | 组件唯一ID |
| `weight` | number | ❌ | 相对权重（用于Row/Column布局，类似于CSS flex-grow） |
| `component` | object | ✅ | 组件内容，包含组件类型和属性 |

### JSON Schema 定义

```json
{
  "surfaceUpdate": {
    "type": "object",
    "properties": {
      "surfaceId": { "type": "string" },
      "components": {
        "type": "array",
        "minItems": 1,
        "items": {
          "type": "object",
          "properties": {
            "id": { "type": "string" },
            "weight": { "type": "number" },
            "component": { "type": "object" }
          },
          "required": ["id", "component"]
        }
      }
    },
    "required": ["surfaceId", "components"]
  }
}
```

### 使用示例

```json
{
  "surfaceUpdate": {
    "surfaceId": "restaurant_list",
    "components": [
      {
        "id": "header",
        "component": { "text": { "text": "餐厅列表", "style": "heading" } }
      },
      {
        "id": "restaurant_1",
        "weight": 1,
        "component": {
          "card": {
            "title": "川菜馆",
            "subtitle": "评分: 4.5",
            "onClick": "view_details_1"
          }
        }
      },
      {
        "id": "restaurant_2",
        "weight": 1,
        "component": {
          "card": {
            "title": "粤菜馆",
            "subtitle": "评分: 4.8",
            "onClick": "view_details_2"
          }
        }
      }
    ]
  }
}
```

---

## 3. dataModelUpdate - 更新数据模型

**作用**：更新指定UI表面的数据模型，用于数据绑定

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `surfaceId` | string | ✅ | UI表面的唯一标识符 |
| `path` | string | ❌ | 数据模型中的路径（如 `/user/name`），默认为 `/` |
| `contents` | array | ✅ | 数据条目数组 |

### 数据条目结构

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `key` | string | ✅ | 数据键名 |
| `valueString` | string | | 字符串值 |
| `valueNumber` | number | | 数值 |
| `valueBoolean` | boolean | | 布尔值 |
| `valueMap` | array | | 映射表（邻接表形式） |

### JSON Schema 定义

```json
{
  "dataModelUpdate": {
    "type": "object",
    "properties": {
      "surfaceId": { "type": "string" },
      "path": { "type": "string" },
      "contents": {
        "type": "array",
        "items": {
          "type": "object",
          "properties": {
            "key": { "type": "string" },
            "valueString": { "type": "string" },
            "valueNumber": { "type": "number" },
            "valueBoolean": { "type": "boolean" },
            "valueMap": {
              "type": "array",
              "items": {
                "type": "object",
                "properties": {
                  "key": { "type": "string" },
                  "valueString": { "type": "string" },
                  "valueNumber": { "type": "number" },
                  "valueBoolean": { "type": "boolean" }
                },
                "required": ["key"]
              }
            }
          },
          "required": ["key"]
        }
      }
    },
    "required": ["contents", "surfaceId"]
  }
}
```

### 使用示例

```json
{
  "dataModelUpdate": {
    "surfaceId": "restaurant_list",
    "path": "/",
    "contents": [
      { "key": "username", "valueString": "张三" },
      { "key": "age", "valueNumber": 28 },
      { "key": "isLoggedIn", "valueBoolean": true },
      { "key": "restaurants", "valueMap": [
        { "key": "r1", "valueString": "川菜馆" },
        { "key": "r2", "valueString": "粤菜馆" },
        { "key": "r3", "valueString": "火锅店" }
      ]}
    ]
  }
}
```

---

## 4. deleteSurface - 删除表面

**作用**：删除指定的UI表面

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `surfaceId` | string | ✅ | 要删除的UI表面ID |

### JSON Schema 定义

```json
{
  "deleteSurface": {
    "type": "object",
    "properties": {
      "surfaceId": { "type": "string" }
    },
    "required": ["surfaceId"]
  }
}
```

### 使用示例

```json
{
  "deleteSurface": {
    "surfaceId": "main_surface"
  }
}
```

---

## 设计特点

### 1. 声明式
A2UI是一种声明式的数据格式，不是可执行代码。客户端只渲染预批准的组件，确保安全性。

### 2. 增量更新
支持四种操作，可以增量更新UI，无需重建整个界面，提高性能和用户体验。

### 3. 数据驱动
通过 `dataModelUpdate` 实现数据与UI的分离，支持数据绑定，UI组件可以引用数据模型中的值。

### 4. 组件化
通过 `surfaceUpdate` 更新组件树，支持复杂的UI组合，组件可以嵌套形成树形结构。

### 5. 轻量级
Schema简洁明了，易于LLM理解和生成，适合作为AI代理的输出格式。

---

## 完整消息示例

一个典型的A2UI消息序列可能如下：

```json
// 步骤1: 开始渲染
{
  "beginRendering": {
    "surfaceId": "booking_form",
    "root": "form_root"
  }
}

// 步骤2: 更新组件
{
  "surfaceUpdate": {
    "surfaceId": "booking_form",
    "components": [
      { "id": "form_root", "component": { "column": { "children": ["title", "inputs", "submit_btn"] } } },
      { "id": "title", "component": { "text": { "text": "预订餐厅", "style": "heading" } } },
      { "id": "inputs", "component": { "column": { "children": ["name_input", "date_input", "guests_input"] } } },
      { "id": "name_input", "component": { "textField": { "label": "姓名", "placeholder": "请输入您的姓名" } } },
      { "id": "date_input", "component": { "textField": { "label": "日期", "placeholder": "选择日期" } } },
      { "id": "guests_input", "component": { "textField": { "label": "人数", "placeholder": "用餐人数" } } },
      { "id": "submit_btn", "component": { "button": { "label": "提交预订", "onClick": "submit_booking" } } }
    ]
  }
}

// 步骤3: 更新数据
{
  "dataModelUpdate": {
    "surfaceId": "booking_form",
    "contents": [
      { "key": "form_title", "valueString": "餐厅预订表单" }
    ]
  }
}
```
