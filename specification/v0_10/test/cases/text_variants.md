# text_variants.json 说明文档

## 文件概述

本文件包含针对 **Text 组件 variant 属性**的测试用例集合，验证 Text 组件的 `variant` 字段是否正确约束为预定义的枚举值。

## 测试目标

验证 Text 组件的 `variant` 字段只接受合法的枚举值（`h1`、`h2`、`h3`、`h4`、`h5`、`caption`、`body`），非法值应被拒绝。

## 测试场景与预期结果

| # | 测试描述 | 预期结果 | 说明 |
|---|---------|---------|------|
| 1 | 有效的 variant 值 `h1` | ✅ 通过 | `variant` 为合法枚举值 |
| 2 | 无效的 variant 值 | ❌ 失败 | `variant` 为 `"not_a_variant"`，不在允许的枚举范围内 |

## 示例

### 有效：variant 为 h1

```json
{
  "version": "v0.10",
  "updateComponents": {
    "surfaceId": "test_surface",
    "components": [
      {
        "id": "text_h1",
        "component": "Text",
        "text": "Header",
        "variant": "h1"
      }
    ]
  }
}
```

### 无效：variant 不在枚举范围内

```json
{
  "version": "v0.10",
  "updateComponents": {
    "surfaceId": "test_surface",
    "components": [
      {
        "id": "text_invalid",
        "component": "Text",
        "text": "Invalid",
        "variant": "not_a_variant"
      }
    ]
  }
}
```
