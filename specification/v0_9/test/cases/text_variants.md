# text_variants.json 说明文档

## 文件概述

本文件是 Text 组件变体（variant）验证测试用例，用于验证 `server_to_client.json` 中 `Text` 组件的 `variant` 字段是否符合 schema 定义的枚举值约束。

## 测试目标

验证 Text 组件的 `variant` 属性：
- 合法的变体值（如 `h1`）应通过校验
- 不在枚举范围内的非法变体值应被拒绝

## 数据结构

| 字段 | 类型 | 说明 |
|------|------|------|
| schema | string | 被测试的 schema 文件，固定为 `server_to_client.json` |
| tests | array | 测试用例数组，每项包含 description、valid 和 data |

### tests 数组元素

| 字段 | 类型 | 说明 |
|------|------|------|
| description | string | 测试用例的描述信息 |
| valid | boolean | 该测试数据是否应通过 schema 校验 |
| data | object | 测试数据，遵循 server_to_client 消息格式 |

### Text 组件字段

| 字段 | 类型 | 说明 |
|------|------|------|
| id | string | 组件唯一标识 |
| component | string | 组件类型，固定为 `Text` |
| text | string | 文本内容 |
| variant | string | 变体类型，须为 schema 定义的枚举值之一（如 h1、h2、body 等） |

## 测试场景与预期结果

| 测试描述 | 预期结果 | 说明 |
|----------|----------|------|
| Text with valid variant 'h1' | ✅ 通过 | variant 为合法枚举值 `h1`，符合 schema 定义 |
| Text with invalid variant | ❌ 失败 | variant 为 `not_a_variant`，不在允许的枚举值范围内 |
