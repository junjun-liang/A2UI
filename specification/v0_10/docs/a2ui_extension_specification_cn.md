# A2UI (Agent-to-Agent UI) 扩展规范 v0.10

## 概述

本扩展实现了 A2UI (Agent-to-Agent UI) 规范 v0.10，一种供 Agent 向客户端发送流式、交互式用户界面的格式。

## 扩展 URI

本扩展的 URI 为 https://a2ui.org/a2a-extension/a2ui/v0.10

这是本扩展唯一接受的 URI。

## 核心概念

A2UI 扩展基于以下主要概念构建：

Surface：一个 "Surface" 是客户端 UI 中独立的、可控制的区域。规范使用 surfaceId 将更新定向到特定的 Surface（例如，主内容区域、侧边面板或新的聊天气泡）。这允许单个 Agent 流独立管理多个 UI 区域。

目录定义文档：a2ui 扩展与目录无关。所有 UI 组件（例如 Text、Row、Button）和函数（例如 required、email）都在单独的目录定义模式中定义。这允许客户端和服务器协商使用哪个目录。

模式：a2ui 扩展由几个主要 JSON 模式定义：

- 目录定义模式：定义组件和函数库的标准格式。
- 服务器到客户端消息列表模式：从 Agent 发送到客户端的消息的核心传输格式（例如 updateComponents、updateDataModel）。
- 客户端到服务器消息列表模式：从客户端发送到 Agent 的消息的核心传输格式（例如 action）。
- 服务器能力模式：`a2uiServerCapabilities` 对象的模式，供服务器声明其 UI 生成能力。
- 客户端能力模式：`a2uiClientCapabilities` 对象的模式。

客户端能力：客户端在 `a2uiClientCapabilities` 对象中将其能力发送给服务器。此对象包含在从客户端发送到服务器的每个 A2A `Message` 的 `metadata` 字段中。此对象允许客户端声明它支持哪些目录。

## Agent Card 详情

Agent 在其 AgentCard 的 `AgentCapabilities.extensions` 列表中通告其 A2UI 能力。`params` 对象定义了 Agent 的特定 UI 支持，并直接对应**服务器能力模式**（`server_capabilities.json`）。

AgentExtension 块示例：

```json
{
  "uri": "https://a2ui.org/a2a-extension/a2ui/v0.10",
  "description": "Ability to render A2UI v0.10",
  "required": false,
  "params": {
    "supportedCatalogIds": [
      "https://a2ui.org/specification/v0_10/basic_catalog.json",
      "https://my-company.com/a2ui/v0_1/my_custom_catalog.json"
    ],
    "acceptsInlineCatalogs": true
  }
}
```

### 参数定义
`params` 对象对应 `server_capabilities.json` 模式中的 `v0.10` 对象：
- `params.supportedCatalogIds`：（可选）字符串数组，每个字符串是标识 Agent 可以生成的目录定义模式的 ID。这不一定是可解析的 URI。
- `params.acceptsInlineCatalogs`：（可选）布尔值，指示 Agent 是否可以接受客户端 `a2uiClientCapabilities` 中的 `inlineCatalogs` 数组。如果省略，默认为 `false`。

## 扩展激活
客户端通过传输层定义的 A2A 扩展激活机制来表明使用 A2UI 扩展的意愿。

对于 JSON-RPC 和 HTTP 传输，通过 X-A2A-Extensions HTTP 头指示。

对于 gRPC，通过 X-A2A-Extensions 元数据值指示。

激活此扩展意味着服务器可以发送 A2UI 特定消息（如 updateComponents），客户端应发送 A2UI 特定事件（如 action）。

## 数据编码

A2UI 消息编码为 A2A `DataPart`。

要将 `DataPart` 标识为包含 A2UI 数据，它必须具有以下元数据：

- `mimeType`：`application/json+a2ui`

`DataPart` 的 `data` 字段包含 A2UI JSON 消息的**列表**（例如 `createSurface`、`updateComponents`、`action`）。它必须是消息数组。

### 处理规则

`data` 字段包含消息列表。此列表**不是**事务单元。接收者（客户端和 Agent）必须按顺序处理列表中的消息。

如果列表中的某条消息验证或应用失败（例如，由于模式违规或无效引用），接收者应报告/记录该特定消息的错误，并且必须继续处理列表中的其余消息。

原子性仅在**单条消息**级别保证。然而，为了更好的用户体验，渲染器不应在列表中的所有消息处理完毕之前重新绘制 UI。这可以防止中间状态对用户闪烁。

### 服务器到客户端消息

当 Agent 向客户端（或充当客户端/渲染器的另一个 Agent）发送消息时，`data` 载荷必须根据**服务器到客户端消息列表模式**验证。

DataPart 示例：

```json
{
  "data": [
    {
      "version": "v0.10",
      "createSurface": {
        "surfaceId": "example_surface",
        "catalogId": "https://a2ui.org/specification/v0_10/basic_catalog.json"
      }
    },
    {
      "version": "v0.10",
      "updateComponents": {
        "surfaceId": "example_surface",
        "components": [
          {
            "component": "Text",
            "id": "root",
            "text": "Hello!"
          }
        ]
      }
    }
  ],
  "kind": "data",
  "metadata": {
    "mimeType": "application/json+a2ui"
  }
}
```

### 客户端到服务器事件

当客户端（或转发事件的 Agent）向 Agent 发送消息时，也使用具有相同 `application/json+a2ui` MIME 类型的 `DataPart`。但是，`data` 载荷必须根据**客户端到服务器消息列表模式**验证。

`action` DataPart 示例：

```json
{
  "data": [
    {
      "version": "v0.10",
      "action": {
        "name": "submit_form",
        "surfaceId": "contact_form_1",
        "sourceComponentId": "submit_button",
        "timestamp": "2026-01-15T12:00:00Z",
        "context": {
          "email": "user@example.com"
        }
      }
    }
  ],
  "kind": "data",
  "metadata": {
    "mimeType": "application/json+a2ui"
  }
}
```
