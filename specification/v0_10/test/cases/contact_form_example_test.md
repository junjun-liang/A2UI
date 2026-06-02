# contact_form_example_test.json 说明文档

## 文件概述

本文件包含一个**联系人表单的完整示例测试**，演示了 A2UI 协议的典型使用流程：创建表面 → 更新组件 → 更新数据模型。

## 测试目标

验证一个完整的联系人表单 UI 构建流程，展示如何通过 A2UI 消息动态创建包含多种组件类型的交互式表单。

## 测试场景与预期结果

| # | 测试描述 | 预期结果 | 说明 |
|---|---------|---------|------|
| 1 | 创建联系人表单表面 | ✅ 通过 | 使用 `createSurface` 创建新表面 |
| 2 | 更新联系人表单组件 | ✅ 通过 | 使用 `updateComponents` 定义完整的表单组件树 |
| 3 | 更新数据模型 | ✅ 通过 | 使用 `updateDataModel` 初始化表单数据 |

## 表单结构说明

测试中的联系人表单包含以下组件层次：

- **root** (Column) - 垂直布局容器
  - first_name_label (Text) - "First Name"
  - first_name_field (TextField) - 绑定 `/contact/firstName`
  - last_name_label (Text) - "Last Name"
  - last_name_field (TextField) - 绑定 `/contact/lastName`
  - email_label (Text) - "Email"
  - email_field (TextField) - 绑定 `/contact/email`，带邮箱校验
  - phone_label (Text) - "Phone"
  - phone_field (TextField) - 绑定 `/contact/phone`
  - notes_label (Text) - "Notes"
  - notes_field (TextField) - 绑定 `/contact/notes`，`variant: "longText"`
  - submit_button (Button) - 提交按钮，触发 `submitContactForm` 事件

## 示例

### 创建表面

```json
{
  "version": "v0.10",
  "createSurface": {
    "surfaceId": "contact_form_1",
    "catalogId": "https://a2ui.org/specification/v0_10/basic_catalog.json"
  }
}
```

### 更新数据模型

```json
{
  "version": "v0.10",
  "updateDataModel": {
    "surfaceId": "contact_form_1",
    "path": "/contact",
    "value": {
      "firstName": "John",
      "lastName": "Doe",
      "email": "john.doe@example.com"
    }
  }
}
```
