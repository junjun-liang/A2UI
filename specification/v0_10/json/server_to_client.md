# server_to_client.json 说明文档

## 文件概述

本文件定义了 A2UI（Agent to UI）协议中**服务端到客户端**的消息模式（JSON Schema）。它描述了服务端如何通过消息动态创建、更新和删除用户界面，以及如何从服务端调用客户端函数。

## 数据结构

顶层结构是一个对象，通过 `oneOf` 约束为以下五种消息类型之一：

| 消息类型 | 说明 |
|---------|------|
| `CreateSurfaceMessage` | 创建新的 UI 表面 |
| `UpdateComponentsMessage` | 更新表面的组件树 |
| `UpdateDataModelMessage` | 更新表面的数据模型 |
| `DeleteSurfaceMessage` | 删除已有表面 |
| `CallFunctionMessage` | 从服务端调用客户端函数 |

所有消息类型均要求包含 `version` 字段，值为 `"v0.10"`。

## 字段说明

### CreateSurfaceMessage

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `version` | const (`"v0.10"`) | 是 | 协议版本号 |
| `createSurface` | object | 是 | 创建表面的指令 |
| `createSurface.surfaceId` | string | 是 | UI 表面的唯一标识符 |
| `createSurface.catalogId` | string | 是 | 组件目录的唯一标识符，建议使用域名前缀避免冲突 |
| `createSurface.theme` | object | 否 | 初始主题参数（如 `{"primaryColor": "#FF0000"}`），需符合目录中定义的 theme 模式 |
| `createSurface.sendDataModel` | boolean | 否 | 若为 true，客户端在每次发送 A2A 消息时将附带该表面的完整数据模型，默认 false |

### UpdateComponentsMessage

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `version` | const (`"v0.10"`) | 是 | 协议版本号 |
| `updateComponents` | object | 是 | 更新组件的指令 |
| `updateComponents.surfaceId` | string | 是 | 要更新的表面 ID |
| `updateComponents.components` | array | 是 | 组件列表，至少包含 1 项，其中必须有一个组件的 `id` 为 `"root"` |

### UpdateDataModelMessage

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `version` | const (`"v0.10"`) | 是 | 协议版本号 |
| `updateDataModel` | object | 是 | 更新数据模型的指令 |
| `updateDataModel.surfaceId` | string | 是 | 目标表面 ID |
| `updateDataModel.path` | string | 否 | 数据模型中的路径（如 `/user/name`），省略或设为 `/` 表示整个数据模型 |
| `updateDataModel.value` | any | 否 | 要更新的值；若存在则替换/创建，若省略则删除该路径的键 |

### DeleteSurfaceMessage

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `version` | const (`"v0.10"`) | 是 | 协议版本号 |
| `deleteSurface` | object | 是 | 删除表面的指令 |
| `deleteSurface.surfaceId` | string | 是 | 要删除的表面 ID |

### CallFunctionMessage

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `version` | const (`"v0.10"`) | 是 | 协议版本号 |
| `functionCallId` | object (CallId) | 是 | 函数调用的唯一 ID，必须在响应中原样返回 |
| `wantResponse` | boolean | 否 | 是否需要客户端返回函数结果，默认 false |
| `callFunction` | object (FunctionCall) | 是 | 函数调用详情 |
| `callFunction.call` | string | 是 | 要调用的函数名称 |
| `callFunction.returnType` | string | 是 | 函数返回类型 |
| `callFunction.callableFrom` | enum | 是 | 调用来源限制：`remoteOnly` 或 `clientOrRemote` |
| `callFunction.args` | object | 否 | 传递给函数的参数 |

## 示例

### 创建表面

```json
{
  "version": "v0.10",
  "createSurface": {
    "surfaceId": "main_surface",
    "catalogId": "https://a2ui.org/specification/v0_10/basic_catalog.json",
    "theme": {
      "primaryColor": "#00BFFF"
    },
    "sendDataModel": true
  }
}
```

### 更新组件

```json
{
  "version": "v0.10",
  "updateComponents": {
    "surfaceId": "main_surface",
    "components": [
      { "id": "root", "component": "Column", "children": ["txt1"] },
      { "id": "txt1", "component": "Text", "text": "Hello" }
    ]
  }
}
```

### 服务端调用客户端函数

```json
{
  "version": "v0.10",
  "functionCallId": { "callId": "call-001" },
  "wantResponse": true,
  "callFunction": {
    "call": "openUrl",
    "args": { "url": "https://example.com" },
    "returnType": "void",
    "callableFrom": "clientOrRemote"
  }
}
```
