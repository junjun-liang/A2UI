# tabs_checks.json 说明文档

## 文件概述

本文件包含针对 **Tabs 组件校验**的测试用例集合，验证 `server_to_client.json` 模式中 Tabs 组件的 `tabs` 数组约束。

## 测试目标

验证 Tabs 组件的 `tabs` 数组不能为空，且每个 tab 必须包含 `title` 和 `child` 字段。

## 测试场景与预期结果

| # | 测试描述 | 预期结果 | 说明 |
|---|---------|---------|------|
| 1 | Tabs 空数组 | ❌ 失败 | `tabs` 数组为空，违反 `minItems: 1` 约束 |
| 2 | Tabs 有效数组 | ✅ 通过 | `tabs` 包含至少一个 tab，每个 tab 有 `title` 和 `child` |

## 示例

### 无效：空 tabs 数组

```json
{
  "version": "v0.10",
  "updateComponents": {
    "surfaceId": "test_surface",
    "components": [
      {
        "id": "tabs_empty",
        "component": "Tabs",
        "tabs": []
      }
    ]
  }
}
```

### 有效：包含 tab 内容

```json
{
  "version": "v0.10",
  "updateComponents": {
    "surfaceId": "test_surface",
    "components": [
      {
        "id": "tabs_valid",
        "component": "Tabs",
        "tabs": [
          { "title": "Tab 1", "child": "txt1" }
        ]
      },
      { "id": "txt1", "component": "Text", "text": "Tab 1 content" }
    ]
  }
}
```
