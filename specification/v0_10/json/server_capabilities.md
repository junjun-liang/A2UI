# server_capabilities.json 说明文档

## 文件概述

本文件定义了 A2UI 协议中**服务端能力声明**的模式。服务端（或代理）通过此对象向客户端声明其支持的 UI 功能，可嵌入到 A2A 协议的 Agent Card 中，或用于 MCP 等其他传输协议。

## 数据结构

顶层为对象，包含按协议版本组织的能力声明。

## 字段说明

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `v0.10` | object | 是 | v0.10 版本的服务端能力结构 |
| `v0.10.supportedCatalogIds` | array\<string\> | 否 | 服务端能够生成的目录定义模式 ID 数组，不一定是可解析的 URI |
| `v0.10.acceptsInlineCatalogs` | boolean | 否 | 服务端是否接受客户端能力中的 `inlineCatalogs` 数组，默认 false |

## 示例

```json
{
  "v0.10": {
    "supportedCatalogIds": [
      "https://a2ui.org/specification/v0_10/basic_catalog.json"
    ],
    "acceptsInlineCatalogs": true
  }
}
```
