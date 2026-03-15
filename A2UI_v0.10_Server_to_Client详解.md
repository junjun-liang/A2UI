# A2UI v0.10 服务器到客户端消息Schema详解

> 本文件详解 `/specification/v0_10/json/server_to_client.json`

---

## 概述

`server_to_client.json` 是 A2UI v0.10 中定义**服务器到客户端消息**的Schema。这些消息由服务器（代理）发送给客户端，用于动态构建和更新用户界面。

---

## Schema 结构

```json
{
  "type": "object",
  "oneOf": [
    { "$ref": "#/$defs/CreateSurfaceMessage" },
    { "$ref": "#/$defs/UpdateComponentsMessage" },
    { "$ref": "#/$defs/UpdateDataModelMessage" },
    { "$ref": "#/$defs/DeleteSurfaceMessage" },
    { "$ref": "#/$defs/CallFunctionMessage" }
  ],
  "$defs": {
    "CreateSurfaceMessage": { ... },
    "UpdateComponentsMessage": { ... },
    "UpdateDataModelMessage": { ... },
    "DeleteSurfaceMessage": { ... },
    "CallFunctionMessage": { ... }
  }
}
```

**规则**：每个消息必须是以下五种类型之一

---

## 1. CreateSurfaceMessage - 创建表面消息

通知客户端创建一个新的UI表面。

### Schema

```json
{
  "type": "object",
  "properties": {
    "version": { "const": "v0.10" },
    "createSurface": {
      "properties": {
        "surfaceId": { "type": "string" },
        "catalogId": { "type": "string" },
        "theme": { "$ref": "catalog.json#/$defs/theme" },
        "sendDataModel": { "type": "boolean" }
      },
      "required": ["surfaceId", "catalogId"]
    }
  },
  "required": ["createSurface", "version"]
}
```

### 字段说明

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `version` | const | ✅ | 固定值 `"v0.10"` |
| `createSurface.surfaceId` | string | ✅ | UI表面唯一ID |
| `createSurface.catalogId` | string | ✅ | 组件目录ID |
| `createSurface.theme` | object | ❌ | 主题参数 |
| `createSurface.sendDataModel` | boolean | ❌ | 是否发送数据模型 |

### 示例

```json
{
  "version": "v0.10",
  "createSurface": {
    "surfaceId": "restaurant_booking",
    "catalogId": "https://a2ui.org/specification/v0_10/basic_catalog.json",
    "theme": {
      "primaryColor": "#FF5722",
      "agentDisplayName": "餐厅助手"
    },
    "sendDataModel": true
  }
}
```

---

## 2. UpdateComponentsMessage - 更新组件消息

更新UI表面的组件树。

### Schema

```json
{
  "type": "object",
  "properties": {
    "version": { "const": "v0.10" },
    "updateComponents": {
      "properties": {
        "surfaceId": { "type": "string" },
        "components": { "type": "array" }
      },
      "required": ["surfaceId", "components"]
    }
  },
  "required": ["updateComponents", "version"]
}
```

### 字段说明

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `version` | const | ✅ | 固定值 `"v0.10"` |
| `updateComponents.surfaceId` | string | ✅ | UI表面ID |
| `updateComponents.components` | array | ✅ | 组件列表 |

### 规则

- 组件列表中**必须**有一个组件的 `id` 为 `"root"`
- `surfaceId` 必须是之前通过 `createSurface` 创建的

### 示例

```json
{
  "version": "v0.10",
  "updateComponents": {
    "surfaceId": "restaurant_booking",
    "components": [
      {
        "id": "root",
        "component": "Column",
        "children": ["header", "form"]
      },
      {
        "id": "header",
        "component": "Text",
        "text": { "literalString": "餐厅预订" },
        "variant": "h1"
      },
      {
        "id": "form",
        "component": "Column",
        "children": ["name_field", "date_field", "submit_btn"]
      }
    ]
  }
}
```

---

## 3. UpdateDataModelMessage - 更新数据模型消息

更新UI表面的数据模型。

### Schema

```json
{
  "type": "object",
  "properties": {
    "version": { "const": "v0.10" },
    "updateDataModel": {
      "properties": {
        "surfaceId": { "type": "string" },
        "path": { "type": "string" },
        "value": { "additionalProperties": true }
      },
      "required": ["surfaceId"]
    }
  },
  "required": ["updateDataModel", "version"]
}
```

### 字段说明

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `version` | const | ✅ | 固定值 `"v0.10"` |
| `updateDataModel.surfaceId` | string | ✅ | UI表面ID |
| `updateDataModel.path` | string | ❌ | 数据路径（默认 `/`） |
| `updateDataModel.value` | any | ❌ | 要更新的值 |

### path 说明

- `/` 或省略：替换整个数据模型
- `/user/name`：更新 `user` 下的 `name` 字段

### 示例

```json
{
  "version": "v0.10",
  "updateDataModel": {
    "surfaceId": "restaurant_booking",
    "path": "/",
    "value": {
      "name": "",
      "date": "",
      "guests": 2
    }
  }
}
```

---

## 4. DeleteSurfaceMessage - 删除表面消息

删除指定的UI表面。

### Schema

```json
{
  "type": "object",
  "properties": {
    "version": { "const": "v0.10" },
    "deleteSurface": {
      "properties": {
        "surfaceId": { "type": "string" }
      },
      "required": ["surfaceId"]
    }
  },
  "required": ["deleteSurface", "version"]
}
```

### 字段说明

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `version` | const | ✅ | 固定值 `"v0.10"` |
| `deleteSurface.surfaceId` | string | ✅ | 要删除的UI表面ID |

### 示例

```json
{
  "version": "v0.10",
  "deleteSurface": {
    "surfaceId": "restaurant_booking"
  }
}
```

---

## 5. CallFunctionMessage - 调用函数消息

服务器调用客户端的函数。

### Schema

```json
{
  "type": "object",
  "properties": {
    "version": { "const": "v0.10" },
    "functionCallId": {
      "properties": {
        "agentId": { "type": "string" },
        "callId": { "type": "string" }
      },
      "required": ["callId"]
    },
    "wantResponse": { "type": "boolean" },
    "callFunction": {
      "properties": {
        "callableFrom": { "enum": ["remoteOnly", "clientOrRemote"] },
        "call": { "type": "string" },
        "args": { "type": "object" },
        "returnType": { "enum": ["array", "boolean", "number", "object", "string", "void"] }
      },
      "required": ["call", "returnType", "callableFrom"]
    }
  },
  "required": ["version", "callFunction", "functionCallId"]
}
```

### 字段说明

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `version` | const | ✅ | 固定值 `"v0.10"` |
| `functionCallId.callId` | string | ✅ | 函数调用唯一ID |
| `functionCallId.agentId` | string | ❌ | 代理ID |
| `wantResponse` | boolean | ❌ | 是否需要响应 |
| `callFunction.call` | string | ✅ | 函数名称 |
| `callFunction.args` | object | ❌ | 函数参数 |
| `callFunction.returnType` | enum | ✅ | 返回值类型 |
| `callFunction.callableFrom` | enum | ✅ | 调用来源 |

### callableFrom 说明

| 值 | 说明 |
|-----|------|
| `remoteOnly` | 仅服务器可调用 |
| `clientOrRemote` | 客户端和服务器都可调用 |

### 示例

```json
{
  "version": "v0.10",
  "functionCallId": {
    "agentId": "restaurant_agent",
    "callId": "call_abc123"
  },
  "wantResponse": true,
  "callFunction": {
    "callableFrom": "clientOrRemote",
    "call": "getLocation",
    "args": {
      "enableHighAccuracy": true
    },
    "returnType": "object"
  }
}
```

---

## 完整消息流程示例

```jsonl
{"version": "v0.10", "createSurface": {"surfaceId": "main", "catalogId": "https://a2ui.org/specification/v0_10/basic_catalog.json"}}
{"version": "v0.10", "updateComponents": {"surfaceId": "main", "components": [{"id": "root", "component": "Column", "children": ["title"]}, {"id": "title", "component": "Text", "text": {"literalString": "Hello"}}]}}
{"version": "v0.10", "updateDataModel": {"surfaceId": "main", "path": "/", "value": {"name": "User"}}}
{"version": "v0.10", "deleteSurface": {"surfaceId": "main"}}
```

---

## Schema 结构图

```
server_to_client.json
├── CreateSurfaceMessage
│   ├── version: "v0.10" (required)
│   └── createSurface
│       ├── surfaceId (required)
│       ├── catalogId (required)
│       ├── theme (optional)
│       └── sendDataModel (optional)
│
├── UpdateComponentsMessage
│   ├── version: "v0.10" (required)
│   └── updateComponents
│       ├── surfaceId (required)
│       └── components[] (required, must have id="root")
│
├── UpdateDataModelMessage
│   ├── version: "v0.10" (required)
│   └── updateDataModel
│       ├── surfaceId (required)
│       ├── path (optional)
│       └── value (optional)
│
├── DeleteSurfaceMessage
│   ├── version: "v0.10" (required)
│   └── deleteSurface
│       └── surfaceId (required)
│
└── CallFunctionMessage
    ├── version: "v0.10" (required)
    ├── functionCallId
    │   ├── agentId (optional)
    │   └── callId (required)
    ├── wantResponse (optional)
    └── callFunction
        ├── callableFrom (required)
        ├── call (required)
        ├── args (optional)
        └── returnType (required)
```

---

## 与其他Schema的关系

| Schema | 关系 |
|--------|------|
| `client_to_server.json` | 客户端响应 action/functionResponse/error |
| `common_types.json` | 定义 CallId、FunctionCall 等类型 |
| `basic_catalog.json` | 组件定义引用此目录 |
| `client_data_model.json` | sendDataModel 相关 |

---

## 总结

A2UI v0.10 的服务器到客户端消息包含5种核心操作：

1. **createSurface** - 创建UI表面
2. **updateComponents** - 更新组件树
3. **updateDataModel** - 更新数据模型
4. **deleteSurface** - 删除UI表面
5. **callFunction** - 调用客户端函数

每条消息必须包含 `"version": "v0.10"` 和恰好一个操作类型。
