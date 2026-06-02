# tabs_checks.json 说明文档

## 文件概述

本文件是 Tabs 组件验证测试用例，用于验证 `server_to_client.json` 中 `Tabs` 组件的 `tabs` 字段是否符合 schema 定义。

## 测试目标

验证 Tabs 组件的 `tabs` 数组约束，确保：
- `tabs` 数组不允许为空（至少包含一个选项卡）
- `tabs` 数组包含有效的选项卡定义时能通过校验

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

### Tabs 组件字段

| 字段 | 类型 | 说明 |
|------|------|------|
| id | string | 组件唯一标识 |
| component | string | 组件类型，固定为 `Tabs` |
| tabs | array | 选项卡数组，每项包含 title（标题）和 child（子组件引用） |

## 测试场景与预期结果

| 测试描述 | 预期结果 | 说明 |
|----------|----------|------|
| Tabs with empty tabs array | ❌ 失败 | tabs 数组为空，不满足最小元素数量约束 |
| Tabs with valid tabs array | ✅ 通过 | tabs 数组包含有效的选项卡定义（含 title 和 child） |
