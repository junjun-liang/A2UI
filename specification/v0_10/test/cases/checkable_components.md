# checkable_components.json 说明文档

## 文件概述

本文件包含针对**可校验组件（Checkable）**的测试用例集合，验证 TextField、ChoicePicker、Slider、CheckBox、DateTimeInput 等组件的 `checks` 校验功能。

## 测试目标

验证支持 `checks` 属性的各类输入组件的校验规则是否正确执行，包括基础校验、组合逻辑校验和无效校验结构的拒绝。

## 测试场景与预期结果

### 基础校验

| # | 测试描述 | 预期结果 | 说明 |
|---|---------|---------|------|
| 1 | TextField 带邮箱校验 | ✅ 通过 | 使用 `required` 和 `email` 校验 |
| 2 | ChoicePicker 带长度校验 | ✅ 通过 | 使用 `length` 校验确保至少选择一项 |
| 3 | Slider 带数值范围校验 | ✅ 通过 | 使用 `numeric` 校验 |
| 4 | CheckBox 带 required 校验 | ✅ 通过 | 使用 `required` 校验 |
| 5 | DateTimeInput 带 required 校验 | ✅ 通过 | 使用 `required` 校验 |
| 6 | TextField 带 regex 校验 | ✅ 通过 | 使用正则校验手机号格式 |
| 7 | TextField 带长度范围校验 | ✅ 通过 | 使用 `length` 校验密码长度 |
| 8 | Slider 带数值范围校验 | ✅ 通过 | 使用 `numeric` 校验分数范围 |

### 组合逻辑校验

| # | 测试描述 | 预期结果 | 说明 |
|---|---------|---------|------|
| 9 | TextField 带复杂逻辑（AND/OR/NOT） | ✅ 通过 | 使用 `and`、`or`、`not` 组合多个校验条件 |

### 无效校验结构

| # | 测试描述 | 预期结果 | 说明 |
|---|---------|---------|------|
| 10 | 缺少 message | ❌ 失败 | CheckRule 中 `message` 为必填字段 |
| 11 | 函数 returnType 不为 boolean | ❌ 失败 | `formatString` 的 `returnType` 为 `string`，校验条件必须返回 `boolean` |
| 12 | ChoicePicker 精确选择数量 | ✅ 通过 | 使用 `length` 校验确保恰好选择 2 项 |

## 示例

### TextField 带邮箱校验

```json
{
  "id": "tf1",
  "component": "TextField",
  "label": "Email",
  "value": { "path": "/formData/email" },
  "checks": [
    {
      "condition": { "call": "required", "args": { "value": { "path": "/formData/email" } }, "returnType": "boolean" },
      "message": "Email is required"
    },
    {
      "condition": { "call": "email", "args": { "value": { "path": "/formData/email" } }, "returnType": "boolean" },
      "message": "Must be valid email"
    }
  ]
}
```

### 复杂逻辑校验（AND/OR/NOT）

```json
{
  "id": "tf_complex",
  "component": "TextField",
  "label": "Secret Code",
  "value": { "path": "/formData/code" },
  "checks": [
    {
      "condition": {
        "call": "and",
        "args": {
          "values": [
            { "call": "required", "args": { "value": { "path": "/formData/code" } }, "returnType": "boolean" },
            { "call": "or", "args": { "values": [
              { "call": "regex", "args": { "value": { "path": "/formData/code" }, "pattern": "^[A-Z]" }, "returnType": "boolean" },
              { "call": "not", "args": { "value": { "call": "regex", "args": { "value": { "path": "/formData/code" }, "pattern": "^[0-9]" }, "returnType": "boolean" } }, "returnType": "boolean" }
            ]}, "returnType": "boolean" }
          ]
        },
        "returnType": "boolean"
      },
      "message": "Code must start with letter or not start with number"
    }
  ]
}
```
