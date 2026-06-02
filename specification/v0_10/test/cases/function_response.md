# function_response.json 说明文档

## 文件概述

本文件包含针对**函数响应消息（functionResponse）**的测试用例集合，验证 `client_to_server.json` 模式中 `functionResponse` 字段的校验逻辑。

## 测试目标

验证函数响应消息的结构正确性，包括 `functionCallId` 的必填约束和 `value` 字段支持的各种数据类型。

## 测试场景与预期结果

| # | 测试描述 | 预期结果 | 说明 |
|---|---------|---------|------|
| 1 | 有效的函数响应（对象值） | ✅ 通过 | `value` 为包含嵌套字段的对象 |
| 2 | 缺少 callId | ❌ 失败 | `functionCallId` 为必填字段 |
| 3 | 标量字符串值 | ✅ 通过 | `value` 为字符串 |
| 4 | 标量数字值 | ✅ 通过 | `value` 为数字 |
| 5 | 标量布尔值 | ✅ 通过 | `value` 为布尔值 |
| 6 | 标量 null 值 | ✅ 通过 | `value` 为 null |
| 7 | 数组值 | ✅ 通过 | `value` 为数组，包含混合类型元素 |
| 8 | 嵌套对象值 | ✅ 通过 | `value` 包含嵌套对象 |
| 9 | 数组包含嵌套对象 | ✅ 通过 | `value` 为数组，其中元素包含嵌套对象 |

## 示例

### 有效的函数响应（对象值）

```json
{
  "version": "v0.10",
  "functionResponse": {
    "functionCallId": { "callId": "unique-call-id-130" },
    "value": { "result": "success", "count": 42 }
  }
}
```

### 有效的函数响应（null 值）

```json
{
  "version": "v0.10",
  "functionResponse": {
    "functionCallId": { "callId": "unique-call-id-131d" },
    "value": null
  }
}
```
