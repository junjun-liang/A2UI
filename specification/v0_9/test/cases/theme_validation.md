# theme_validation.json 说明文档

## 文件概述

本文件是主题验证测试用例，用于验证 `server_to_client.json` 中 `createSurface` 消息的 `theme` 字段是否符合 basic_catalog 的主题 schema 定义。

## 测试目的

验证主题属性的类型约束和格式约束是否正确执行，确保客户端能正确校验服务端发送的主题参数。

## 测试场景与预期结果

| 测试描述 | 预期结果 | 说明 |
|----------|----------|------|
| Valid theme in createSurface | ✅ 通过 | 有效的主题属性：primaryColor 为合法 hex 颜色，agentDisplayName 为字符串 |
| Invalid theme property (wrong type) | ❌ 失败 | primaryColor 类型错误：传入了数字 123 而非字符串 |
| Invalid theme property (invalid hex color) | ❌ 失败 | primaryColor 格式错误：不匹配 `^#[0-9a-fA-F]{6}$` 正则 |
| Additional theme properties are allowed | ✅ 通过 | 主题允许额外属性（additionalProperties: true），customProperty 应被接受 |

## 数据结构

每个测试用例包含：
- **schema**: 被测试的 schema 文件
- **tests**: 测试数组，每项包含 description（描述）、valid（预期是否通过）和 data（测试数据）
