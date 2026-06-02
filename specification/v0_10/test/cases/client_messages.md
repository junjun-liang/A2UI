# client_messages.json 说明文档

## 文件概述

本文件包含针对**客户端到服务端消息**的测试用例集合，验证 `client_to_server.json` 模式中 `action`、`error` 等消息类型的校验逻辑。

## 测试目标

验证客户端到服务端消息的结构正确性，包括操作事件、验证失败错误和通用错误的格式约束。

## 测试场景与预期结果

| # | 测试描述 | 预期结果 | 说明 |
|---|---------|---------|------|
| 1 | 有效的操作消息 | ✅ 通过 | 包含完整的 action 字段 |
| 2 | 有效的验证失败错误 | ✅ 通过 | `code` 为 `VALIDATION_FAILED`，包含 `surfaceId`、`path`、`message` |
| 3 | 无效的 updateDataModel（已重命名） | ❌ 失败 | `updateDataModel` 不是客户端到服务端消息的有效字段 |
| 4 | 错误：有效的 functionCallId | ✅ 通过 | 通用错误中包含 `functionCallId` |
| 5 | 错误：同时包含 callId 和 surfaceId | ❌ 失败 | 通用错误中 `functionCallId` 和 `surfaceId` 不能同时存在 |
| 6 | 错误：既无 callId 也无 surfaceId | ❌ 失败 | 通用错误中必须提供 `functionCallId` 或 `surfaceId` 之一 |

## 示例

### 有效的操作消息

```json
{
  "version": "v0.10",
  "action": {
    "name": "submit",
    "surfaceId": "main",
    "sourceComponentId": "btn_submit",
    "timestamp": "2023-10-27T10:00:00Z",
    "context": { "foo": "bar" }
  }
}
```

### 错误：同时包含 callId 和 surfaceId（应失败）

```json
{
  "version": "v0.10",
  "error": {
    "code": "FUNCTION_FAILED",
    "functionCallId": { "callId": "unique-call-id-133" },
    "surfaceId": "main",
    "message": "Something went wrong"
  }
}
```
