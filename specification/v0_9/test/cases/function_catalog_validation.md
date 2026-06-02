# function_catalog_validation.json 说明文档

## 文件概述

本文件是函数目录（Function Catalog）验证测试用例，用于验证 `server_to_client.json` 中各类内置函数调用（`call`）的参数类型、返回值类型及约束条件是否符合 schema 定义。

## 测试目标

验证 A2UI 协议中所有内置函数的调用规范，包括：
- 验证类函数：`required`、`regex`、`length`、`numeric`、`email`
- 格式化类函数：`formatString`、`formatNumber`、`formatCurrency`、`formatDate`、`pluralize`
- 动作类函数：`openUrl`
- 逻辑类函数：`and`、`or`、`not`

确保每个函数的参数类型、必填字段、返回值类型均被正确校验，非法调用应被拒绝。

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

## 测试场景与预期结果

### 验证类函数

| 测试描述 | 预期结果 | 说明 |
|----------|----------|------|
| required: Valid call | ✅ 通过 | 有效的 required 调用，包含 value 参数和 boolean 返回类型 |
| required: Invalid args (empty) | ❌ 失败 | 缺少必填参数 value |
| required: Invalid returnType | ❌ 失败 | returnType 应为 boolean，传入了 string |
| required: Too many arguments | ❌ 失败 | 传入了多余的 extra 参数 |
| regex: Valid call | ✅ 通过 | 有效的 regex 调用，包含 value 和 pattern 参数 |
| regex: Invalid args (missing pattern) | ❌ 失败 | 缺少必填参数 pattern |
| regex: Invalid pattern type (number) | ❌ 失败 | pattern 应为字符串，传入了数字 |
| regex: Invalid returnType | ❌ 失败 | returnType 应为 boolean，传入了 string |
| length: Valid min | ✅ 通过 | 有效的 length 调用，仅指定 min |
| length: Valid max | ✅ 通过 | 有效的 length 调用，仅指定 max |
| length: Invalid constraint (empty object) | ❌ 失败 | 未指定 min 或 max，缺少约束条件 |
| length: Invalid min type (string) | ❌ 失败 | min 应为整数，传入了字符串 |
| length: Invalid max value (negative) | ❌ 失败 | max 值为负数，不合法 |
| numeric: Valid range | ✅ 通过 | 有效的 numeric 调用，指定 min 和 max |
| numeric: Invalid min type (string) | ❌ 失败 | min 应为数字，传入了字符串 |
| numeric: Invalid max type (string) | ❌ 失败 | max 应为数字，传入了字符串 |
| email: Valid call | ✅ 通过 | 有效的 email 调用 |
| email: Invalid args count (too many) | ❌ 失败 | 传入了多余的 extra 参数 |

### 格式化类函数

| 测试描述 | 预期结果 | 说明 |
|----------|----------|------|
| formatString: Valid call | ✅ 通过 | 有效的 formatString 调用，value 含模板变量 |
| formatString: Invalid returnType | ❌ 失败 | returnType 应为 string，传入了 boolean |
| formatString: Invalid format string type (number) | ❌ 失败 | value 应为字符串，传入了数字 |
| formatNumber: Valid args | ✅ 通过 | 有效的 formatNumber 调用，含 decimals 和 grouping |
| formatNumber: Invalid args (wrong type for precision) | ❌ 失败 | decimals 应为整数，传入了字符串 |
| formatNumber: Invalid precision type (boolean) | ❌ 失败 | decimals 应为整数，传入了布尔值 |
| formatCurrency: Valid args | ✅ 通过 | 有效的 formatCurrency 调用，含 currency 代码 |
| formatCurrency: Missing currency code | ❌ 失败 | 缺少必填参数 currency |
| formatCurrency: Invalid currency code type (number) | ❌ 失败 | currency 应为字符串，传入了数字 |
| formatDate: Valid args | ✅ 通过 | 有效的 formatDate 调用，含 format 模式 |
| formatDate: Invalid pattern type (null) | ❌ 失败 | format 应为字符串，传入了 null |
| pluralize: Valid args | ✅ 通过 | 有效的 pluralize 调用，含 one 和 other |
| pluralize: Invalid (missing 'other') | ❌ 失败 | 缺少必填参数 other |

### 动作类函数

| 测试描述 | 预期结果 | 说明 |
|----------|----------|------|
| openUrl: Valid call in Action | ✅ 通过 | 有效的 openUrl 调用，在 Button 的 action 中使用 |
| openUrl: Invalid args (string instead of object) | ❌ 失败 | args 应为对象，传入了字符串 |
| openUrl: Invalid returnType | ❌ 失败 | returnType 应为 void，传入了 boolean |
| openUrl: Invalid URL format (not a URI) | ❌ 失败 | url 格式不合法，非有效 URI |

### 逻辑类函数

| 测试描述 | 预期结果 | 说明 |
|----------|----------|------|
| and: Valid call | ✅ 通过 | 有效的 and 调用，values 包含多个布尔值 |
| and: Invalid (single value) | ❌ 失败 | values 数组至少需要 2 个元素 |
| and: Invalid returnType | ❌ 失败 | returnType 应为 boolean，传入了 string |
| or: Valid call | ✅ 通过 | 有效的 or 调用，values 包含多个布尔值 |
| or: Invalid (single value) | ❌ 失败 | values 数组至少需要 2 个元素 |
| not: Valid call | ✅ 通过 | 有效的 not 调用，value 为布尔值 |
| not: Invalid argument type (string) | ❌ 失败 | value 应为布尔值，传入了字符串 |
| not: Invalid returnType | ❌ 失败 | returnType 应为 boolean，传入了 string |
