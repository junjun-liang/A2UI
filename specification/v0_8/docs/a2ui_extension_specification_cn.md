# A2UI（Agent-to-Agent UI）扩展规范

## 概述

本扩展实现了 A2UI（Agent-to-Agent UI）规范，一种供代理向客户端发送流式、交互式用户界面的格式。

## 扩展 URI

本扩展的 URI 为 https://a2ui.org/a2a-extension/a2ui/v0.8

这是本扩展唯一接受的 URI。

## 核心概念

A2UI 扩展基于以下主要概念构建：

Surface："Surface" 是客户端 UI 中一个独立的、可控制的区域。规范使用 surfaceId 将更新定向到特定的 surface（如主内容区域、侧边面板或新的聊天气泡）。这允许单个代理流独立管理多个 UI 区域。

Catalog Definition Document：a2ui 扩展是组件无关的。所有 UI 组件（如 Text、Row、Button）及其样式都在单独的 Catalog Definition Schema 中定义。这允许客户端和服务端协商使用哪个 catalog。

Schema：a2ui 扩展由三个主要 JSON schema 定义：

Catalog Definition Schema：用于定义组件库和样式的标准格式。

Server-to-Client Message Schema：从代理发送到客户端的消息的核心线路格式（如 surfaceUpdate、dataModelUpdate）。

Client-to-Server Event Schema：从客户端发送到代理的消息的核心线路格式（如 userAction）。

客户端能力：客户端在 `a2uiClientCapabilities` 对象中向服务端发送其能力。此对象包含在从客户端发送到服务端的每条 A2A `Message` 的 `metadata` 字段中。此对象允许客户端声明它支持哪些 catalog。

## Agent Card 详情

代理在 AgentCard 的 `AgentCapabilities.extensions` 列表中通告其 A2UI 能力。`params` 对象定义了代理的特定 UI 支持。

AgentExtension 块示例：

```json
{
  "uri": "https://a2ui.org/a2a-extension/a2ui/v0.8",
  "description": "Ability to render A2UI",
  "required": false,
  "params": {
    "supportedCatalogIds": [
      "https://a2ui.org/specification/v0_8/standard_catalog_definition.json",
      "https://my-company.com/a2ui/v0.8/my_custom_catalog.json"
    ],
    "acceptsInlineCatalogs": true
  }
}
```

### 参数定义
- `params.supportedCatalogIds`：（可选）字符串数组，每个字符串是指向代理可以生成的组件 Catalog Definition Schema 的 URI。
- `params.acceptsInlineCatalogs`：（可选）布尔值，指示代理是否可以接受客户端 `a2uiClientCapabilities` 中的 `inlineCatalogs` 数组。如果省略，默认为 `false`。

## 扩展激活
客户端通过传输层定义的 A2A 扩展激活机制来表明使用 A2UI 扩展的意愿。

对于 JSON-RPC 和 HTTP 传输，通过 X-A2A-Extensions HTTP 头指示。

对于 gRPC，通过 X-A2A-Extensions 元数据值指示。

激活此扩展意味着服务端可以发送 A2UI 特定的消息（如 surfaceUpdate），客户端应发送 A2UI 特定的事件（如 userAction）。

## 数据编码

A2UI 消息编码为 A2A `DataPart`。

要将 `DataPart` 标识为包含 A2UI 数据，它必须具有以下元数据：

- `mimeType`：`application/json+a2ui`

`DataPart` 的 `data` 字段包含 A2UI JSON 消息（如 `surfaceUpdate`、`userAction`）。

A2UI DataPart 示例：

```json
{
  "data": {
    "beginRendering": {
      "surfaceId": "outlier_stores_map_surface",
    }
  },
  "kind": "data",
  "metadata": {
    "mimeType": "application/json+a2ui"
  }
}
```
