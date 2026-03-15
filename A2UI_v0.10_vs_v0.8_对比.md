# A2UI v0.10 vs v0.8 核心变化对比

> 本文件分析 A2UI v0.10 相比 v0.8 版本的主要变化

## 概述

A2UI v0.10 是 v0.8 的重大升级版本，在消息格式、组件定义和功能上都有重要改进。

---

## 核心变化对比

### v0.8 核心操作

| 操作 | 作用 |
|------|------|
| `beginRendering` | 开始渲染新的UI表面 |
| `surfaceUpdate` | 更新UI表面的组件列表 |
| `dataModelUpdate` | 更新UI表面的数据模型 |
| `deleteSurface` | 删除指定的UI表面 |

### v0.10 核心操作

| 操作 | 作用 | 对应v0.8 |
|------|------|---------|
| `createSurface` | 创建新的UI表面 | `beginRendering` |
| `updateComponents` | 更新组件 | `surfaceUpdate` |
| `updateDataModel` | 更新数据模型 | `dataModelUpdate` |
| `deleteSurface` | 删除表面 | `deleteSurface` |
| **`callFunction`** | **调用函数** | **🆕 新增** |

---

## 主要变化详解

### 1. 版本标识 (version)

**v0.8**: 无版本标识

**v0.10**: 每个消息必须包含 `version: "v0.10"`

```json
// v0.10 必填
{
  "version": "v0.10",
  "createSurface": { ... }
}
```

---

### 2. createSurface (原 beginRendering)

#### 增强的属性

| 属性 | v0.8 | v0.10 | 说明 |
|------|:----:|:-----:|------|
| `surfaceId` | ✅ | ✅ | UI表面ID |
| `root` | ✅ | ❌ | 根组件ID（已移除） |
| `catalogId` | ❌ | ✅ | 组件目录ID（必填） |
| `styles` | ❌ | → `theme` | 样式 → 主题 |
| `theme` | ❌ | 🆕 | 主题参数 |
| `sendDataModel` | ❌ | 🆕 | 是否发送数据模型 |

#### v0.10 新增

```json
{
  "version": "v0.10",
  "createSurface": {
    "surfaceId": "main_surface",
    "catalogId": "https://a2ui.org/specification/v0_10/standard_catalog.json",
    "theme": {
      "primaryColor": "#FF5722",
      "font": "Roboto"
    },
    "sendDataModel": true
  }
}
```

---

### 3. updateComponents (原 surfaceUpdate)

#### 变化

| 属性 | v0.8 | v0.10 | 说明 |
|------|:----:|:-----:|------|
| `surfaceId` | ✅ | ✅ | UI表面ID |
| `components` | ✅ | ✅ | 组件列表 |
| 组件定义 | 内联 | `$ref` | 引用外部catalog.json |

#### v0.10 组件引用

```json
{
  "version": "v0.10",
  "updateComponents": {
    "surfaceId": "main_surface",
    "components": [
      {
        "id": "root",
        "component": {
          "Column": { ... }  // 引用 catalog.json 中的定义
        }
      }
    ]
  }
}
```

---

### 4. updateDataModel (原 dataModelUpdate)

#### 变化

| 属性 | v0.8 | v0.10 | 说明 |
|------|:----:|:-----:|------|
| `surfaceId` | ✅ | ✅ | UI表面ID |
| `path` | ✅ | ✅ | 数据路径 |
| `contents` | ✅ | ❌ | 数据条目数组 |
| `value` | ❌ | 🆕 | 任意类型的值 |

#### v0.10 简化结构

```json
// v0.8
{
  "dataModelUpdate": {
    "surfaceId": "main",
    "path": "/",
    "contents": [
      { "key": "name", "valueString": "张三" }
    ]
  }
}

// v0.10 - 更灵活
{
  "version": "v0.10",
  "updateDataModel": {
    "surfaceId": "main",
    "path": "/user",
    "value": {
      "name": "张三",
      "age": 30
    }
  }
}
```

---

### 5. 🆕 callFunction (新增)

**v0.10 新增功能**：服务器可以直接调用客户端的函数。

```json
{
  "version": "v0.10",
  "functionCallId": "call_123",
  "wantResponse": true,
  "callFunction": {
    "call": {
      "name": "getLocation",
      "arguments": {}
    },
    "returnType": {
      "type": "object",
      "properties": {
        "latitude": { "type": "number" },
        "longitude": { "type": "number" }
      }
    },
    "callableFrom": "clientOrRemote"
  }
}
```

#### 字段说明

| 字段 | 类型 | 说明 |
|------|------|------|
| `functionCallId` | string | 函数调用ID |
| `wantResponse` | boolean | 是否需要响应 |
| `callFunction.call.name` | string | 函数名称 |
| `callFunction.call.arguments` | object | 函数参数 |
| `callFunction.returnType` | object | 返回类型定义 |
| `callFunction.callableFrom` | enum | 调用来源 |

#### callableFrom 选项

- `remoteOnly` - 仅服务器可调用
- `clientOrRemote` - 客户端和服务器都可调用

---

## Schema 结构对比

### v0.8

```
server_to_client.json
├── beginRendering
│   ├── surfaceId ✓
│   ├── catalogId (可选)
│   ├── root ✓
│   └── styles
├── surfaceUpdate
│   ├── surfaceId ✓
│   └── components[]
├── dataModelUpdate
│   ├── surfaceId ✓
│   ├── path
│   └── contents[]
└── deleteSurface
    └── surfaceId ✓
```

### v0.10

```
server_to_client.json
├── version: "v0.10" (必填)
├── createSurface
│   ├── surfaceId ✓
│   ├── catalogId ✓
│   ├── theme
│   └── sendDataModel
├── updateComponents
│   ├── surfaceId ✓
│   └── components[] (引用catalog.json)
├── updateDataModel
│   ├── surfaceId ✓
│   ├── path
│   └── value (任意类型)
├── deleteSurface
│   └── surfaceId ✓
└── callFunction (🆕新增)
    ├── functionCallId ✓
    ├── wantResponse
    └── callFunction ✓
```

---

## 完整消息对比

### v0.8 完整示例

```json
{
  "beginRendering": {
    "surfaceId": "restaurant_list",
    "root": "root_component"
  },
  "surfaceUpdate": {
    "surfaceId": "restaurant_list",
    "components": [
      {
        "id": "root_component",
        "component": {
          "Column": {
            "children": ["title", "list"]
          }
        }
      },
      {
        "id": "title",
        "component": {
          "Text": {
            "text": { "literalString": "餐厅列表" },
            "usageHint": "h1"
          }
        }
      }
    ]
  }
}
```

### v0.10 完整示例

```json
{
  "version": "v0.10",
  "createSurface": {
    "surfaceId": "restaurant_list",
    "catalogId": "https://a2ui.org/specification/v0_10/standard_catalog.json",
    "theme": {
      "primaryColor": "#FF5722"
    }
  }
}
---
{
  "version": "v0.10",
  "updateComponents": {
    "surfaceId": "restaurant_list",
    "components": [
      {
        "id": "root",
        "component": {
          "Column": {
            "children": ["title", "list"]
          }
        }
      },
      {
        "id": "title",
        "component": {
          "Text": {
            "text": { "literalString": "餐厅列表" },
            "style": "heading1"
          }
        }
      }
    ]
  }
}
```

---

## 总结：v0.10 主要改进

| 改进点 | 说明 |
|--------|------|
| **版本标识** | 明确版本号，确保协议兼容 |
| **callFunction** | 🆕 新增函数调用功能，支持更丰富的交互 |
| **组件目录引用** | 组件定义引用外部catalog.json，解耦更清晰 |
| **数据模型简化** | 直接使用value字段，更灵活直观 |
| **主题系统** | 正式引入theme概念，支持更强大的样式定制 |
| **sendDataModel** | 支持客户端主动发送数据模型到服务器 |

v0.10 相比 v0.8 是一个更成熟、更灵活的协议版本，特别是在函数调用和组件目录管理方面有重大改进。
