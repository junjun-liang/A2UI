# testing_catalog.json 说明文档

## 文件概述

本文件定义了 A2UI 协议中用于**测试目的的目录**（Catalog）。它是一个精简版的目录，仅包含 `Text` 组件和两个函数（`openUrl`、`pingServer`），用于验证协议消息的校验逻辑。

## 数据结构

| 部分 | 说明 |
|------|------|
| `components` | 组件定义集合（仅 Text） |
| `functions` | 函数定义集合（openUrl、pingServer） |
| `$defs` | 辅助定义（主题、组件/函数联合类型） |

## 组件定义

### Text

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `id` | string (ComponentId) | 是 | 组件唯一标识符 |
| `component` | const (`"Text"`) | 是 | 组件类型标识 |
| `text` | DynamicString | 是 | 要显示的文本内容 |

## 函数定义

### openUrl

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `callableFrom` | const (`"clientOrRemote"`) | — | 可从客户端或远程调用 |
| `call` | const (`"openUrl"`) | 是 | 函数名称 |
| `args.url` | string (uri) | 是 | 要打开的 URL |
| `returnType` | const (`"void"`) | — | 无返回值 |

### pingServer

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `callableFrom` | const (`"remoteOnly"`) | — | 仅可从远程调用 |
| `call` | const (`"pingServer"`) | 是 | 函数名称 |
| `returnType` | const (`"void"`) | — | 无返回值 |

## 主题定义 ($defs.theme)

| 字段 | 类型 | 说明 |
|------|------|------|
| `primaryColor` | string | 主品牌色，十六进制格式 |
| `iconUrl` | string (uri) | 图标 URL |
| `agentDisplayName` | string | 代理显示名称 |

## 示例

```json
{
  "catalogId": "https://a2ui.org/specification/v0_10/testing_catalog.json",
  "components": {
    "Text": { ... }
  },
  "functions": {
    "openUrl": { ... },
    "pingServer": { ... }
  }
}
```
