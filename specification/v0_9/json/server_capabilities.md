# server_capabilities.json 说明文档

## 文件概述

本文件定义了 A2UI 服务端能力声明（Server Capabilities）的 JSON Schema。A2UI 服务端（或 Agent）通过此对象向客户端声明其支持的 UI 功能特性。该对象可以嵌入到 A2A 的 Agent Card 中，也可用于 MCP 等其他传输协议。

## 数据结构

顶层为对象类型，包含一个版本化的能力声明字段 `v0.9`，该字段为必填项。

## 字段说明

### 顶层字段

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| v0.9 | object | 是 | A2UI 协议 v0.9 版本的服务端能力结构 |

### v0.9 对象字段

| 字段名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| supportedCatalogIds | string[] | 否 | - | 服务端能够生成的目录定义 Schema ID 数组，每个字符串标识一个目录，不一定是可解析的 URI |
| acceptsInlineCatalogs | boolean | 否 | false | 指示服务端是否接受客户端在 `a2uiClientCapabilities` 中提供的内联目录（inlineCatalogs）数组 |

## 示例

```json
{
  "v0.9": {
    "supportedCatalogIds": [
      "https://a2ui.org/specification/v0_9/basic_catalog.json",
      "https://a2ui.org/specification/v0_9/minimal_catalog.json"
    ],
    "acceptsInlineCatalogs": true
  }
}
```
