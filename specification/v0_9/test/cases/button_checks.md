# button_checks.json 说明文档

## 文件概述

本文件是 Button 组件验证测试用例，用于验证 `server_to_client.json` 中 `Button` 组件的 `checks`、`variant`、`enabled`、`primary` 等字段是否符合 schema 定义。

## 测试目标

验证 Button 组件的多项约束：
- 嵌套 checks 条件（含逻辑组合 and/or）的正确性
- 已废弃属性（`enabled`、`primary`）应被拒绝
- checks 中非法结构（无效 returnType、多余属性）应被拒绝
- variant 枚举值（`primary`、`borderless`）的合法性

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

### Button 组件字段

| 字段 | 类型 | 说明 |
|------|------|------|
| id | string | 组件唯一标识 |
| component | string | 组件类型，固定为 `Button` |
| child | string | 子组件引用 ID |
| action | object | 按钮动作，包含 event 或 functionCall |
| variant | string | 按钮变体，如 `primary`、`borderless` |
| checks | array | 校验条件数组，决定按钮是否可用 |
| enabled | object | **已废弃**，不应再使用 |
| primary | boolean | **已废弃**，不应再使用 |

## 测试场景与预期结果

| 测试描述 | 预期结果 | 说明 |
|----------|----------|------|
| Button with nested valid checks | ✅ 通过 | 有效的嵌套 checks，使用 and/or 逻辑组合 required 条件 |
| Button with deprecated enabled property | ❌ 失败 | 使用了已废弃的 `enabled` 属性，应被拒绝 |
| Button with invalid check structure (invalid returnType) | ❌ 失败 | checks 中 returnType 为非法值 `not_a_valid_type` |
| Button with invalid nested structure (extra property) | ❌ 失败 | checks 项包含不允许的额外属性 `extraProp` |
| Button with variant 'primary' | ✅ 通过 | variant 为合法枚举值 `primary` |
| Button with variant 'borderless' | ✅ 通过 | variant 为合法枚举值 `borderless` |
| Button with deprecated 'primary' property | ❌ 失败 | 使用了已废弃的 `primary` 布尔属性，应被拒绝 |
