# server_to_client.json 说明文档

## 文件概述

本文件定义了 A2UI（Agent to UI）服务端到客户端消息的 JSON Schema。该消息用于动态构建和更新用户界面，是 A2UI 协议的核心消息格式。每条消息必须是以下四种类型之一：创建界面、更新组件、更新数据模型、删除界面。

## 数据结构

顶层为对象类型，通过 `oneOf` 约束为以下四种消息类型之一：

1. **CreateSurfaceMessage** — 创建新界面
2. **UpdateComponentsMessage** — 更新界面组件
3. **UpdateDataModelMessage** — 更新数据模型
4. **DeleteSurfaceMessage** — 删除界面

## 字段说明

### CreateSurfaceMessage（创建界面消息）

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| version | string (const: "v0.9") | 是 | 协议版本号 |
| createSurface | object | 是 | 创建界面的指令对象 |
| createSurface.surfaceId | string | 是 | 要渲染的 UI 界面的唯一标识符 |
| createSurface.catalogId | string | 是 | 目录的唯一标识字符串，建议使用自有域名前缀以避免冲突（如 `mycompany.com:somecatalog`） |
| createSurface.theme | object | 否 | 界面的主题参数（如 `{"primaryColor": "#FF0000"}`），必须符合目录中定义的 theme schema |
| createSurface.sendDataModel | boolean | 否 | 若为 true，客户端将在每次发送给创建该界面的服务端的 A2A 消息元数据中附带该界面的完整数据模型，默认为 false |

### UpdateComponentsMessage（更新组件消息）

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| version | string (const: "v0.9") | 是 | 协议版本号 |
| updateComponents | object | 是 | 更新组件的指令对象 |
| updateComponents.surfaceId | string | 是 | 要更新的 UI 界面的唯一标识符 |
| updateComponents.components | array | 是 | 界面的所有 UI 组件列表，至少包含 1 项，其中必须有一个组件的 `id` 为 `root` 作为组件树根节点 |

### UpdateDataModelMessage（更新数据模型消息）

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| version | string (const: "v0.9") | 是 | 协议版本号 |
| updateDataModel | object | 是 | 更新数据模型的指令对象 |
| updateDataModel.surfaceId | string | 是 | 数据模型更新所针对的界面标识符 |
| updateDataModel.path | string | 否 | 数据模型中的路径（如 `/user/name`），省略或设为 `/` 时指整个数据模型 |
| updateDataModel.value | any | 否 | 要更新的数据值。若提供，则替换（或创建）path 处的值；若省略，则删除 path 处的键 |

### DeleteSurfaceMessage（删除界面消息）

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| version | string (const: "v0.9") | 是 | 协议版本号 |
| deleteSurface | object | 是 | 删除界面的指令对象 |
| deleteSurface.surfaceId | string | 是 | 要删除的界面标识符 |

## 示例

### 创建界面

```json
{
  "version": "v0.9",
  "createSurface": {
    "surfaceId": "login-surface",
    "catalogId": "https://a2ui.org/specification/v0_9/basic_catalog.json",
    "theme": { "primaryColor": "#00BFFF" },
    "sendDataModel": true
  }
}
```

### 更新组件

```json
{
  "version": "v0.9",
  "updateComponents": {
    "surfaceId": "login-surface",
    "components": [
      { "id": "root", "component": "Column", "children": ["title", "username", "password", "loginBtn"] },
      { "id": "title", "component": "Text", "text": "欢迎登录" }
    ]
  }
}
```

### 更新数据模型

```json
{
  "version": "v0.9",
  "updateDataModel": {
    "surfaceId": "login-surface",
    "path": "/user/name",
    "value": "Alice"
  }
}
```

### 删除界面

```json
{
  "version": "v0.9",
  "deleteSurface": {
    "surfaceId": "login-surface"
  }
}
```
