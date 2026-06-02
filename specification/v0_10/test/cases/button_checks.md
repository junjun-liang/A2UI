# button_checks.json 说明文档

## 文件概述

本文件包含针对 **Button 组件校验**的测试用例集合，验证 Button 组件的 `checks`、`variant` 属性以及已废弃属性的拒绝逻辑。

## 测试目标

验证 Button 组件的校验规则嵌套、`variant` 枚举约束，以及废弃属性（`enabled`、`primary`）应被拒绝。

## 测试场景与预期结果

| # | 测试描述 | 预期结果 | 说明 |
|---|---------|---------|------|
| 1 | 嵌套有效校验（and/or 组合） | ✅ 通过 | 使用 `and` 和 `or` 逻辑组合多个 `required` 校验 |
| 2 | 废弃的 enabled 属性 | ❌ 失败 | `enabled` 属性已废弃，不允许使用 |
| 3 | 无效校验结构（returnType 错误） | ❌ 失败 | `returnType` 为 `"not_a_valid_type"`，不在枚举范围内 |
| 4 | 无效嵌套结构（额外属性） | ❌ 失败 | CheckRule 中包含不允许的 `extraProp` |
| 5 | variant 为 primary | ✅ 通过 | `variant: "primary"` 为合法枚举值 |
| 6 | variant 为 borderless | ✅ 通过 | `variant: "borderless"` 为合法枚举值 |
| 7 | 废弃的 primary 属性 | ❌ 失败 | `primary` 布尔属性已废弃，应使用 `variant: "primary"` |

## 示例

### 嵌套校验（and/or 组合）

```json
{
  "id": "btn1",
  "component": "Button",
  "child": "txt1",
  "action": { "event": { "name": "submit" } },
  "checks": [
    {
      "condition": {
        "call": "and",
        "args": {
          "values": [
            { "call": "required", "args": { "value": { "path": "/formData/terms" } }, "returnType": "boolean" },
            { "call": "or", "args": { "values": [
              { "call": "required", "args": { "value": { "path": "/formData/email" } }, "returnType": "boolean" },
              { "call": "required", "args": { "value": { "path": "/formData/phone" } }, "returnType": "boolean" }
            ]}, "returnType": "boolean" }
          ]
        },
        "returnType": "boolean"
      },
      "message": "Must accept terms and provide contact info"
    }
  ]
}
```

### 废弃属性（应失败）

```json
{
  "id": "btn_dep",
  "component": "Button",
  "child": "txt_dep",
  "action": { "event": { "name": "submit" } },
  "primary": true
}
```
