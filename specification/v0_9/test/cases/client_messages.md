# client_messages.json 说明文档

## 文件概述

本文件是客户端消息验证测试用例，用于验证 `client_to_server.json` 中客户端到服务端消息的结构正确性。

## 测试目的

验证客户端消息（action 和 error）的 schema 约束是否正确执行，确保非法消息格式被拒绝。

## 测试场景与预期结果

| 测试描述 | 预期结果 | 说明 |
|----------|----------|------|
| Valid action message | ✅ 通过 | 有效的用户动作消息，包含所有必填字段 |
| Valid error message (validation failed) | ❌ 失败 → ✅ 通过 | 有效的验证失败错误消息 |
| Invalid updateDataModel (renamed) | ❌ 失败 | updateDataModel 不是客户端到服务端消息的有效字段，应被拒绝 |

## 数据结构

每个测试用例包含：
- **schema**: 被测试的 schema 文件（client_to_server.json）
- **tests**: 测试数组，每项包含 description、valid 和 data
