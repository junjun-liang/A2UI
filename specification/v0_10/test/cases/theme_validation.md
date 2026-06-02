# theme_validation.json 说明文档

## 文件概述

本文件包含针对 **主题（theme）验证** 的测试用例集合，验证 `createSurface` 消息中 `theme` 属性的校验逻辑。

## 测试目标

验证 `createSurface` 消息中 `theme` 字段的类型约束和格式要求是否正确执行。

## 测试场景与预期结果

| # | 测试描述 | 预期结果 | 说明 |
|---|---------|---------|------|
| 1 | 有效的主题属性 | ✅ 通过 | `primaryColor` 为有效的十六进制颜色值，`agentDisplayName` 为字符串 |
| 2 | 无效的主题属性（类型错误） | ❌ 失败 | `primaryColor` 为数字 123，应为字符串 |
| 3 | 无效的主题属性（无效十六进制颜色） | ❌ 失败 | `primaryColor` 为 `"invalid-color"`，不符合 `^#[0-9a-fA-F]{6}$` 格式 |
| 4 | 允许额外的主题属性 | ✅ 通过 | 在有效属性基础上添加 `customProperty`，因为 theme 允许 `additionalProperties` |

## 示例

### 有效主题

```json
{
  "version": "v0.10",
  "createSurface": {
    "surfaceId": "test_surface",
    "catalogId": "https://a2ui.org/specification/v0_10/basic_catalog.json",
    "theme": {
      "primaryColor": "#00BFFF",
      "agentDisplayName": "Test Agent"
    }
  }
}
```

### 无效主题（类型错误）

```json
{
  "version": "v0.10",
  "createSurface": {
    "surfaceId": "test_surface",
    "catalogId": "https://a2ui.org/specification/v0_10/basic_catalog.json",
    "theme": {
      "primaryColor": 123
    }
  }
}
```
