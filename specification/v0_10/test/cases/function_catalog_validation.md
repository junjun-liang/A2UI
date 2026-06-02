# function_catalog_validation.json 说明文档

## 文件概述

本文件包含针对**目录函数校验**的测试用例集合，验证 `server_to_client.json` 模式中各类函数调用的参数、返回类型和约束是否正确校验。

## 测试目标

验证 `basic_catalog.json` 中定义的所有函数（校验函数、格式化函数、逻辑函数、openUrl）的参数结构和返回类型约束。

## 测试场景与预期结果

### 校验函数

| # | 测试描述 | 预期结果 | 说明 |
|---|---------|---------|------|
| 1 | required：有效调用 | ✅ 通过 | 包含 `value` 参数，`returnType` 为 `boolean` |
| 2 | required：无效参数（空） | ❌ 失败 | 缺少必需的 `value` 参数 |
| 3 | required：无效 returnType | ❌ 失败 | `returnType` 为 `string`，应为 `boolean` |
| 4 | regex：有效调用 | ✅ 通过 | 包含 `value` 和 `pattern` 参数 |
| 5 | regex：缺少 pattern | ❌ 失败 | 缺少必需的 `pattern` 参数 |
| 6 | length：有效 min 约束 | ✅ 通过 | 仅指定 `min` |
| 7 | length：有效 max 约束 | ✅ 通过 | 仅指定 `max` |
| 8 | length：无效（空约束） | ❌ 失败 | 未指定 `min` 或 `max`，至少需要一个 |
| 9 | numeric：有效范围 | ✅ 通过 | 同时指定 `min` 和 `max` |
| 10 | email：有效调用 | ✅ 通过 | 包含 `value` 参数 |

### 格式化函数

| # | 测试描述 | 预期结果 | 说明 |
|---|---------|---------|------|
| 11 | formatString：有效调用 | ✅ 通过 | 包含模板字符串 |
| 12 | formatString：无效 returnType | ❌ 失败 | `returnType` 为 `boolean`，应为 `string` |
| 13 | formatNumber：有效参数 | ✅ 通过 | 包含 `value`、`decimals`、`grouping` |
| 14 | formatNumber：无效精度类型 | ❌ 失败 | `decimals` 为字符串，应为数字 |
| 15 | formatCurrency：有效参数 | ✅ 通过 | 包含 `value` 和 `currency` |
| 16 | formatCurrency：缺少货币代码 | ❌ 失败 | 缺少必需的 `currency` 参数 |
| 17 | formatDate：有效参数 | ✅ 通过 | 包含 `value` 和 `format` |
| 18 | pluralize：有效参数 | ✅ 通过 | 包含 `value` 和 `other` |
| 19 | pluralize：缺少 other | ❌ 失败 | `other` 为必填的回退字符串 |

### openUrl 函数

| # | 测试描述 | 预期结果 | 说明 |
|---|---------|---------|------|
| 20 | openUrl：有效调用 | ✅ 通过 | 在 Action 中使用，`args` 为对象 |
| 21 | openUrl：无效参数类型 | ❌ 失败 | `args` 为字符串而非对象 |
| 22 | openUrl：无效 returnType | ❌ 失败 | `returnType` 为 `boolean`，应为 `void` |

### 类型错误

| # | 测试描述 | 预期结果 | 说明 |
|---|---------|---------|------|
| 23 | length：无效 min 类型（字符串） | ❌ 失败 | `min` 应为整数 |
| 24 | length：无效 max 值（负数） | ❌ 失败 | `max` 不能为负数 |
| 25 | numeric：无效 min 类型 | ❌ 失败 | `min` 应为数字 |
| 26 | numeric：无效 max 类型 | ❌ 失败 | `max` 应为数字 |
| 27 | regex：无效 pattern 类型 | ❌ 失败 | `pattern` 应为字符串 |
| 28 | email：参数过多 | ❌ 失败 | 包含不允许的额外参数 |
| 29 | formatString：无效值类型 | ❌ 失败 | `value` 应为字符串 |
| 30 | formatNumber：无效精度类型（布尔） | ❌ 失败 | `decimals` 应为数字 |
| 31 | formatCurrency：无效货币代码类型 | ❌ 失败 | `currency` 应为字符串 |
| 32 | formatDate：无效 pattern 类型 | ❌ 失败 | `format` 不能为 null |

### 逻辑函数

| # | 测试描述 | 预期结果 | 说明 |
|---|---------|---------|------|
| 33 | and：有效调用 | ✅ 通过 | 至少 2 个布尔值 |
| 34 | and：单个值 | ❌ 失败 | 至少需要 2 个值 |
| 35 | or：有效调用 | ✅ 通过 | 至少 2 个布尔值 |
| 36 | or：单个值 | ❌ 失败 | 至少需要 2 个值 |
| 37 | not：有效调用 | ✅ 通过 | 单个布尔值 |
| 38 | not：无效参数类型 | ❌ 失败 | 参数为字符串而非布尔值 |
| 39 | not：无效 returnType | ❌ 失败 | `returnType` 应为 `boolean` |
| 40 | required：参数过多 | ❌ 失败 | 包含不允许的额外参数 |
| 41 | regex：无效 returnType | ❌ 失败 | `returnType` 应为 `boolean` |
| 42 | and：无效 returnType | ❌ 失败 | `returnType` 应为 `boolean` |

### openUrl 额外校验

| # | 测试描述 | 预期结果 | 说明 |
|---|---------|---------|------|
| 43 | openUrl：无效 URL 格式 | ❌ 失败 | URL 不符合 URI 格式 |
