# call_function_message.json 说明文档

## 文件概述

本文件包含针对**服务端调用客户端函数消息（CallFunctionMessage）**的测试用例集合，使用 `testing_catalog.json` 作为目录进行校验。

## 测试目标

验证 `CallFunctionMessage` 的结构约束，包括 `functionCallId` 必填性、`callableFrom` 限制、`returnType` 约束以及函数是否在目录中定义。

## 测试场景与预期结果

| # | 测试描述 | 预期结果 | 说明 |
|---|---------|---------|------|
| 1 | 有效：带 wantResponse | ✅ 通过 | 调用 `openUrl`，`callableFrom` 为 `clientOrRemote`，包含 `wantResponse: true` |
| 2 | 有效：remoteOnly 函数 | ✅ 通过 | 调用 `pingServer`，`callableFrom` 为 `remoteOnly` |
| 3 | 有效：不带 wantResponse | ✅ 通过 | `wantResponse` 为可选字段 |
| 4 | 缺少 callId | ❌ 失败 | `functionCallId` 为必填字段 |
| 5 | 缺少 callFunction | ❌ 失败 | `callFunction` 为必填字段 |
| 6 | callableFrom 为 clientOnly | ❌ 失败 | 服务端调用中 `callableFrom` 不能为 `clientOnly`，仅允许 `remoteOnly` 或 `clientOrRemote` |
| 7 | 缺少 callableFrom | ❌ 失败 | `callableFrom` 在 CallFunctionMessage 中为必填 |
| 8 | 无效参数（嵌套对象作为单值） | ❌ 失败 | `args.value` 不能为嵌套对象 |
| 9 | 无效参数（嵌套对象数组） | ❌ 失败 | `args.value` 不能为嵌套对象数组 |
| 10 | 无效 returnType（非标量） | ❌ 失败 | `returnType` 为 `object`，不是有效的标量类型 |
| 11 | 调用仅限客户端的函数 | ❌ 失败 | `required` 函数的 `callableFrom` 为 `clientOnly`，不能从服务端调用 |

## 示例

### 有效调用

```json
{
  "version": "v0.10",
  "callFunction": {
    "call": "openUrl",
    "args": { "url": "https://example.com" },
    "returnType": "void",
    "callableFrom": "clientOrRemote"
  },
  "functionCallId": { "callId": "unique-call-id-123" },
  "wantResponse": true
}
```

### 无效：callableFrom 为 clientOnly

```json
{
  "version": "v0.10",
  "callFunction": {
    "call": "required",
    "args": { "value": "bar" },
    "returnType": "boolean",
    "callableFrom": "clientOnly"
  },
  "functionCallId": { "callId": "unique-call-id-126" }
}
```
