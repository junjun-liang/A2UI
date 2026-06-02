# checkable_components.json 说明文档

## 文件概述

本文件是可校验组件（Checkable Components）测试用例，用于验证 `server_to_client.json` 中支持 `checks` 字段的各类组件（TextField、ChoicePicker、Slider、CheckBox、DateTimeInput）在搭配不同验证函数时的结构正确性。

## 测试目标

验证以下可校验组件与验证函数的组合是否符合 schema 定义：
- TextField 搭配 required、email、regex、length、逻辑组合（and/or/not）等验证
- ChoicePicker 搭配 length 验证
- Slider 搭配 numeric 验证
- CheckBox 搭配 required 验证
- DateTimeInput 搭配 required 验证

同时验证非法的 checks 结构（缺少 message、错误的 returnType）应被拒绝。

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

### 涉及的组件类型

| 组件 | 说明 | 支持的验证函数示例 |
|------|------|-------------------|
| TextField | 文本输入框 | required、email、regex、length、and/or/not |
| ChoicePicker | 选择器 | length |
| Slider | 滑块 | numeric |
| CheckBox | 复选框 | required |
| DateTimeInput | 日期时间输入 | required |

### checks 数组元素

| 字段 | 类型 | 说明 |
|------|------|------|
| condition | object | 验证条件，包含 call（函数名）、args（参数）、returnType（返回类型） |
| message | string | 验证失败时的提示信息（必填） |

## 测试场景与预期结果

### 有效测试场景

| 测试描述 | 预期结果 | 说明 |
|----------|----------|------|
| TextField with valid checks | ✅ 通过 | TextField 搭配 required 和 email 验证 |
| ChoicePicker with valid checks | ✅ 通过 | ChoicePicker 搭配 length(min:1) 验证 |
| Slider with valid checks | ✅ 通过 | Slider 搭配 numeric(min:3) 验证 |
| CheckBox with valid checks | ✅ 通过 | CheckBox 搭配 required 验证 |
| DateTimeInput with valid checks | ✅ 通过 | DateTimeInput 搭配 required 验证 |
| TextField with regex validation | ✅ 通过 | TextField 搭配 regex 验证（10 位数字） |
| TextField with min/max length validation | ✅ 通过 | TextField 搭配 length(min:8, max:64) 验证 |
| Slider with min/max numeric validation | ✅ 通过 | Slider 搭配 numeric(min:0, max:100) 验证 |
| TextField with complex logic checks (AND/OR/NOT) | ✅ 通过 | TextField 搭配 and/or/not 嵌套逻辑组合验证 |
| ChoicePicker with length validation (exactly 2) | ✅ 通过 | ChoicePicker 搭配 length(min:2, max:2) 精确数量验证 |

### 无效测试场景

| 测试描述 | 预期结果 | 说明 |
|----------|----------|------|
| TextField with invalid check (missing message) | ❌ 失败 | checks 项缺少必填的 message 字段 |
| TextField with invalid function returnType in check | ❌ 失败 | checks 中 condition 的 returnType 为 string，应为 boolean |
